Problem Statement : We need to do a load test on an API(say Product API) using Gatling. 
The steps for test are : 
1. Call the Authentication API(say Identity API) and get the access token. This token is not related to user , its a service token .
2. Call the target API (say Product API) using the access token. 

But the catch is , as the token is not user specific , we don’t want to call the Authentication API every time for every request.
We want to call the Authentication API(Identity API) only once(for the first request ) , get the token , save it somewhere(say local cache) for future use. 
For subsequent requests , the access token will be fetched from local cache  and used to call target API(say Product API)

Solution :
Basically we need a mechanism which will allow us to share data across multiple sessions/requests
We can achieve cross session data sharing by using a local cache  , which can be implemented by using ConcurrentHashMap . 
We will use 2 ConcurrentHashMap  object  , one for Cache and one for Lock.

So also we have to provide a locking mechanism to check whether cache is set for a key(access token) or not. The logic will be something like below :
IF ( able to get lock on resource )  THEN 
Call Authentication API , get the token , save it in Cache. 
ELSE  
Don’t call Authentication API
END IF
Get the token from Cache
Call Target API

Locking will be implemented using ‘putIfAbsent(key,value)’ method of a Lock (ConcurrentHashMap) object to main.
The ‘putIfAbsent(key,value)’ method returns ‘None’ when we set the ‘value’ for the ‘key’ for first time . We will check this return value to get a lock on Cache object. Please check the ‘LookupCache’ class for complete implementation .

Below is the sample Gatling code :


```scala
package Test
// imports for core
import io.gatling.core.Predef._
import io.gatling.http.Predef._

// imports for scenario timings like : (5 seconds)
import scala.concurrent.duration._

// imports for concurrenthashmap
import scala.collection._
import java.util.concurrent.ConcurrentHashMap
import scala.collection.JavaConverters._

// imports for AtomicInteger
import java.util.concurrent.atomic._

// ====================================================
// JAVA_OPTS='-DIDENTITY_URL=IDENTITY_URL -DCLIENT_ID=CLIENT_ID -DGRANT_TYPE=GRANT_TYPE -DSCOPE=SCOPE -DPASSWORD=PASSWORD -DUSER=USER' ./gatling.sh -s Test.TestLookup
//======================================================

class LookupCache {
  val cache  : concurrent.Map[String,String] = new ConcurrentHashMap() asScala
  val locked : concurrent.Map[String,String]   = new ConcurrentHashMap() asScala

  // First thread to call lock(key) returns true, all subsequent ones will return false
  // putIfAbsent => always returns previous value of 'key' . If 'key' is first time set , then it returns 'None'
  // So , if lock() returns 'true' , it means the previous value is 'None' , it means its the first request we will be processing
  // hence considered as 'we got the lock'. If lock() returns false , means there was some previous value which is not equal to 'None'
  // means this is not the first request we are processing , hence we did not get the lock on resource.
  def lock ( key: String, virtualUser: String ) : Boolean = locked.putIfAbsent(key, virtualUser ) == None

  // only the thread that first called lock(key) can call put, and then only once
  def put( key: String, value: String, virtualUser: String ) =
    if ( locked.get( key ).get == virtualUser )
      if ( cache.get( key ) == None )
        cache.put( key, value )
      else
        throw new Exception( "You can't put more than one value into the cache! " + key )
    else
      throw new Exception( "You have not locked '" + key + "'" )

  // Any thread can call get - will block until a non-null value is stored in the cache
  // WARNING: if the thread that is holding the lock never puts a value, this thread will block forever
  def get( key: String ) = {
    if ( locked.get( key ) == None )
      throw new Exception( "Must lock '" + key + "' before you can get() it" )
    var result : Option[String] = None
    do {
      result = cache.get( key )
      if ( result == None ) Thread.sleep( 100 )
    } while ( result == None )
    result.get
  }
}

object Identity {
  // Create the local cache to hold token
  val tokenCache = new LookupCache()

  // Get the environment varaiable passed in commandline
  val IDENTITY_URL = System.getProperty("IDENTITY_URL")
  val CLIENT_ID = System.getProperty("CLIENT_ID")
  val GRANT_TYPE = System.getProperty("GRANT_TYPE")
  val SCOPE = System.getProperty("SCOPE")
  val PASSWORD = System.getProperty("PASSWORD")
  val USER = System.getProperty("USER")

  def authenticate(key: String,user:String) = 
      doIf(session => tokenCache.lock( key, user) ) {
      exec(http("GetAccessToken")
		  .post(IDENTITY_URL)
    	.body(StringBody(s"""{
		      "client_id":"$CLIENT_ID",
		      "grant_type":"$GRANT_TYPE",
          "scope":"$SCOPE",
		      "password":"$PASSWORD",
		      "username":"$USER"
		    }""")).asJson
		.check(status.is(200))
	  .check(jsonPath("$.access_token").saveAs(key)))
    .exec(session => {
        println(s"Saving the token for user = $user , key = $key in cache ")
        tokenCache.put(key,session(key).as[String],user)
        session
      })
    } 

  def getToken(key: String) = { tokenCache.get(key) }

}
//==================================
class TestLookup extends Simulation {
  
  val hdr = scala.collection.immutable.Map("Cache-Control" -> "no-cache")
  val httpConfig = http
      .inferHtmlResources()
      .headers(hdr)

  val id = new AtomicInteger(0)
  val userIdFeeder = Iterator.continually(scala.collection.immutable.Map("userId" -> id.incrementAndGet()))
  
  val testScenario = scenario("TEST LOOKUP")
     .feed(userIdFeeder)
     // Get the universal token for all users
     .exec(Identity.authenticate("GLOBAL_TOKEN_KEY","GLOBAL_USER"))
     // Fetch the token out of the cache for use in subsequent tests
    .exec( session => {
      val globaltoken = Identity.getToken("GLOBAL_TOKEN_KEY")            
      println (s"This is a dummy test with User-"+ session("userId").as[String] +" with Global token " + globaltoken)
      session
      } )

  setUp( 
    testScenario.inject(
        constantUsersPerSec(1) during (5 seconds)
      ) 
  ).protocols(httpConfig)  
}
```


How to run the script ?  
```
JAVA_OPTS='-DIDENTITY_URL={identity_url} -DCLIENT_ID={client_id} -DGRANT_TYPE={grant_type} -DSCOPE={scope} -DPASSWORD={password} -DUSER={user}' ./gatling.sh -s Test.TestLookup  
```  

In the above command we are passing the required parameters(parameters for Authentication call) as environment variable.  


