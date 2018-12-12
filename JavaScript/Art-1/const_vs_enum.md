# Constant vs Enum in Java Script

Lets first understand the basic difference , irrespective of any programming language:

Both const and enum , helps programmer to make the code understandable , readable.  
It helps us to avoid hard coded values floating around the code base.  

The basic difference  
Ans : The difference is  :  

:one:	enum helps us to group a set of constants  , which is related to some context.  
For example for the status of a database transaction :  

With constants can be written as :  
```
const SUCCESS=1  
const FAILED=0  

var transaction_status = SUCCESS
```

with enum can be written as(by using const keyword in JS) :
```
const DbTransactionStatus = {
                SUCCESS : 1,
                FAILED : 0 ,
} ;

var transaction_status = DbTransactionStatus.SUCCESS;
```

In the above example , we saw that the enum , is more elaborative and self-explanatory.  

:two:	enum also helps to write code with less ambiguity  , Just look at the below example :  
```
const QueryExecutionStatus= {
                SUCCESS=”success”,
                FAILED=”failed”
}
const DbTransactionStatus = {
                SUCCESS : 1,
                FAILED : 0 ,
} ;

var transaction_status = DbTransactionStatus.SUCCESS
var query_status = QueryExecutionStatus.SUCCESS
```

But with constants , the constant names will be lengthier, not easy to use and not easy to memorize:  
```
const DB_TRANSACTION_STATUS_SUCCESS=1
const DB_TRANSACTION_STATUS_FAILED=0
const QUERY_EXECUTION_STATUS_SUCCESS=”success”
const QUERY_EXECUTION_STATUS_FAILED=”failed”

var transaction_status = DB_TRANSACTION_STATUS_SUCCESS 
var query_status = QUERY_EXECUTION_STATUS_SUCCESS
```

In Javascript , we can create nested enums , to create an illusion of namespace , where as simple constants are one liners.  
Lets consider the below example :  
```
const Status = {
                DB : { SUCCESS : 1 , FAIL :2,},
                QUERY : { SUCCESS:”success” ,  FAIL: “fail”,}
}

var db_status = Status.DB.SUCCESS ;
var query_status = Status.QUERY.SUCCESS ;
```

**A bit more on const keyword**    

As per the documentation , it says ::   

*The const declaration creates a read-only reference to a value. It does not mean the value it holds is immutable, solely that the variable identifier can not be reassigned.*  

So lets understand the above statement.  

*“ solely that the variable identifier can not be reassigned”*  

Example :
```
1)	const SUCCESS=1
SUCCESS = 2 ;    => will through error , as it can not be reassigned

2)	const DbTransactionStatus = {
SUCCESS : 1,
FAILED : 0 ,
} ;
DbTransactionStatus = 1  => will through error , as it can not be reassigned
```

*“The const declaration creates a read-only reference to a value. It does not mean the value it holds is immutable “*  

As per the above statement , the value , the const variable holds , can be modified.  
But the reference , that the const variable holds , can not be modified.  
Example :  
```
const DbTransactionStatus = { SUCCESS : 1, FAILED : 0 ,} ;  

/*below is a simple object*/
var stringStatus={ SUCCESS:”success” , FAIL: “fail”,};

DbTransactionStatus = stringStatus ;   => The reference can not be change to point to stringStatus .

DbTransactionStatus.SUCCESS = “success” ;  => This works , it means we can change the value of objects , to which the const variable points
```

So , we saw that a simple constant cannot be changed ```(SUCCESS=2 , error) ```.  

But enum , created with const keyword , can be changed , by changing the value , within it ```(DbTransactionStatus.SUCCESS=”success” , works)```.

**Freezing the Object**    
We can also add new property to the enum.  
```
const DbTransactionStatus = { SUCCESS : 1, FAILED : 0 ,} ;  
DbTransactionStatus.ERROR = 3;   => no exception thrown
alert(DbTransactionStatus.ERROR)   => it prints value 3
```

But , still there is an issue persists. The enum we created , is not an enum in a true sense.  

Because , we are able to change the inside values. So how we will restrict it.  

Here comes Object.freeze() method. Look at the below example :  
```
const DbTransactionStatus = { SUCCESS : 1, FAILED : 0 ,} ;  
Object.freeze(DbTransactionStatus);    => freeze the object
DbTransactionStatus.SUCCESS = “success” ; => will not through any error , but will not modify the value
alert(DbTransactionStatus.SUCCESS)  => it will print the value 1
```

But there is an issue with Object.freeze(). It does not freeze the nested objects.  

Example :  
```
const DbTransactionStatus = { 
SUCCESS : 1, 
FAILED : 0 ,
ERROR : {  
TransactionError:100 , 
LockError:200
},
} ;  
```

In the above example , we have a nested object ERROR with 2 properties.  

Now lets try to freeze the object :  
```
alert(DbTransactionStatus.SUCCESS); => prints 1
alert(DbTransactionStatus.ERROR.TransactionError) => prints 100

Object.freeze(DbTransactionStatus)

DbTransactionStatus.SUCCESS = 10;  => no effect
DbTransactionStatus.ERROR.TransactionError = 150;  => changed

alert(DbTransactionStatus.SUCCESS); => prints 1 , value not changed
alert(DbTransactionStatus.ERROR.TransactionError) => prints 150 , value got changed , even if we have freeze the object.
```

**Serialization and Deserialization**  

We can create enum with empty objects as values.  

Look at below example:  
```
const DbTransactionStatus = {
SUCCESS:{},  
FAILl:{},
};

var v1 = DbTransactionStatus.SUCCESS
var v2 = DbTransactionStatus.FAIL
var v3 = DbTransactionStatus.SUCCESS

if (v1 == v2 )
alert("v1 and v2 are equal")
else
alert("v1 and v2 are not equal")   => this executes
  

if (v1 == v3 )
alert("v1 and v3 are equal")  => this executes
else
alert("v1 and v3 are not equal")
```

But there is a problem with such enum declaration.  
The issue comes into picture  when we do serialization.  
Example :  
```
var v1 = DbTransactionStatus.SUCCESS
var serialized = JSON.stringify(v1);
alert("serialized myObject: " + serialized);

var deserializedObject = JSON.parse(serialized);
alert("deserialized object:" + deserializedObject)

if (v1 == deserializedObject)
  alert("Deserialized object is matching original value v1");
else
  alert("Deserialized object is NOT matching");  => this executes
```

The reason this happens is that upon deserialization JSON.parse creates a new object as the value for SUCCESS.   
This new object is not the same as DbTransactionStatus.SUCCESS  , so the comparisons all fail and we end up in the else branch.  

Happy Learning :smile:
