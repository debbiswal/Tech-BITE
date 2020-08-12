The below code sample shows , how the identity API is called for first user and access token is saved in hashmap.  
For subsequent users, Idenity API is not called and access token is pulled from hashmap.  

```scala
package Test

import io.gatling.core.Predef._
import io.gatling.http.Predef._
import scala.concurrent.duration._
import io.gatling.core.structure.ScenarioBuilder
import io.gatling.http.protocol.HttpProtocolBuilder

import scala.collection.mutable._
import scala.concurrent.duration._

import java.util
import java.io.File
import java.io.PrintWriter
import java.io.FileOutputStream

import scala.collection._
import java.util.concurrent.ConcurrentHashMap
import scala.collection.JavaConverters._

// ====================================================
val IDENTITY_URL = System.getProperty("IDENTITY_URL")
val CLIENT_ID = System.getProperty("CLIENT_ID")
val GRANT_TYPE = System.getProperty("GRANT_TYPE")
val SCOPE = System.getProperty("SCOPE")
val PASSWORD = System.getProperty("PASSWORD")
val USER = System.getProperty("USER")
//======================================================

class LookupCache {
  val cache  : concurrent.Map[String,String] = new ConcurrentHashMap() asScala
  val locked : concurrent.Map[String,String]   = new ConcurrentHashMap() asScala

  // first thread to call lock(key) returns true, all subsequent ones will return false
  def lock ( key: String, who: String ) : Boolean = locked.putIfAbsent(key, who ) == None

  // only the thread that first called lock(key) can call put, and then only once
  def put( key: String, value: String, who: String ) =
    if ( locked.get( key ).get == who )
      if ( cache.get( key ) == None )
        cache.put( key, value )
      else
        throw new Exception( "You can't put more than one value into the cache! " + key )
    else
      throw new Exception( "You have not locked '" + key + "'" )

  // any thread can call get - will block until a non-null value is stored in the cache
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
  val tokenCache = new LookupCache()

  def authenticate = 
      doIf( session => tokenCache.lock( "ACCESS_TOKEN", "IDENTITY_USER") ) {
      exec(http("GetAccessToken")
		  .post("$IDENTITY_URL")
    	.body(StringBody(s"""{
		      "client_id":"$CLIENT_ID",
		      "grant_type":"$GRANT_TYPE",
          "scope":"$SCOPE",
		      "password":"$PASSWORD",
		      "username":"$USER"
		    }""")).asJson
		.check(status.is(200))
	  .check(jsonPath("$.access_token").saveAs("access_token")))
      // save the token into the cache for next time
      .exec( session => {
        println("Saving the token in cache")
        tokenCache.put(
          "ACCESS_TOKEN",
          session("access_token").as[String],
          "IDENTITY_USER"
        )
        session
      })
    }
}
//==================================
class TestLookup extends Simulation {
  
  val hdr = scala.collection.immutable.Map("Cache-Control" -> "no-cache")
  val httpConfig = http
      .inferHtmlResources()
      .headers(hdr)

  val persistScenario = scenario("TEST LOOKUP")
     .exec(Identity.authenticate)
     // pull the token out of the cache for use in subsequent tests
    .exec( session => {
      val token = Identity.tokenCache.get("IDENTITY_TOKEN")
      println ("The token is " + token)
      session
      } )

  setUp( 
    persistScenario.inject(
        constantUsersPerSec(1) during (5 seconds)
      ) 
  ).protocols(httpConfig)
  
}
```


How to run the script ?  
JAVA_OPTS="-DIDENTITY_URL=XXX -DCLIENT_ID=XXX -DGRANT_TYPE=XXX -DSCOPE=XXX -DPASSWORD=XXX -DUSER=XXX" ./gatling.sh -s Test.TestLookup
