[:house:Home](https://github.com/debbiswal/Articles) | [Back](https://github.com/debbiswal/Articles/blob/master/README.md#api-gateway)

In my last article[Retry Pattern | Transient Fault - I](../Art-2/TransientFault-I.md)  I discussed about :  
* What is Transient fault ?  
* Dealing transient faults with Simple Retry pattern  
* Demerits of Simple Retry pattern  
* Overcoming issues of Simple Retry pattern by Retry with Exponential back off pattern  

Also we have started discussing about Long lasting Transient fault.  
So today I will continue the rest part of it.  

Lets Start…..

As per my last article Retry pattern does not help in case of long lasting transient fault.  
In order to overcome this , we need a mechanism which :  
* Monitors the Service call
* Periodically checks whether the Service is available or not and maintains this information
* When the Service is called , based on the availability information(which it has already) :
  * If available , then call the Service and return the result
  * If not available , return error.

It means we need a mechanism like a circuit , which will allow the client request to pass through it.  
And if there is any problem , it will not allow to call the Service and return error immediately.  

**What is a circuit ?**  
A Circuit in an electrical/electronics component , which allows the flow of current through it , from one end to other end.  
A Circuit has two states :  
* *Closed* : In this state current flows  
* *Open* : In this state current does not flow  

In Software world it is implemented as Circuit-Breaker pattern  

**What is Circuit breaker pattern ?**   
* The Circuit Breaker pattern prevents an application from repeatedly trying to execute an operation that's likely to fail.  
* It allows to continue without waiting for the fault to be fixed .  
* It also enables an application to detect whether the fault has been resolved.   
* If the problem appears to have been fixed, the application can try to invoke the operation.  

In simple terms Circuit-Breaker is a state machine and monitoring module/component.  
It remembers its state and changes to other state when any specified event(availability of service) happens.  

*Circuit-Breaker pattern has three states : Closed , Open ,Half-Open*  

The given below diagram shows the states and functionality of Circuit-Breaker.  

![Circuit Breaker Pattern](images/circuitbreaker.png)  

**How it Works ?**  
Please refer to the diagram , where I have clearly explained the job of each state and when the switch happens.  

* Every request passes through Circuit-Breaker to access the Service  
* In Closed state, a Failure counter is maintained and it is increased when Service call fails  
* In Half-Open state , a Success counter is maintained and it is increased when Service call succeeds.  
* It’s the job of Circuit-Breaker to call the Service and return the result.  
* The returned result depends on state of Circuit-Breaker.  
  * If circuit is closed , then call Service and return the Service result  
  * If circuit is Open , then return Error  
  * If circuit is Half-Open , then call Service :  
    * If succeeds , then return Service result
    * If fails , return Error

**What configuration it takes to work ?**  
* Threshold value of Failure count for Closed state  
* Threshold value of Success count for Half-Open state  
* Timeout value of timer for Open state  

**What is threshold in above diagram ?**  
A threshold is a maximum value , which is when reached ,at that time circuit breaker will switch to other state.  

Here in Closed state , when the number of times the Service call is failing reaches the threshold value , it switches to Open state
Similarly , in Half-Open state , when the number of times the Service call succeeds reaches the threshold value , it switches to Closed state  

**What is the use of timer in Open state ?**  
The timer gives a breathing time to the failed Service to recover itself.  
When the timer is on , no call is passed to Service , so Service is not overloaded.  
When the timeout happens , the state changes to Half-Open state , where circuit-breaker gives a try to call the Service.  

**What is the need of Half-Open state ?**  
The half-Open state actually behaves a like a monitor .  
It tries to access to Service and :  
* If Service call fails , then it understands that Service is still not recovered and goes back to Open state , to give some more breathing time.  
* If Service call succeeds , then it increases a counter for success , but does not switch to Closed state immediately  
* If the success counter reached to given threshold value , then it understands that Service is stable now and switches to Closed state.  

**What is the advantage of it ?**
* It helps to fail fast. It means ,it can help to maintain the response time of the system by quickly rejecting a request for an operation that's likely to fail, rather than waiting for the operation to time out, or never return   
* Gives the accessed resource(Service) a breathing time to recover  
* Does not overload the Service , if Service is in failed state  
* The Half-Open state prevents a recovering service from suddenly being flooded with requests. 
  As a service recovers, it might be able to support a limited volume of requests until the recovery is complete, but while recovery is in progress a flood of request can cause the service to time out or fail again.  

**When to use this pattern ?**  
You can use this pattern to prevent an application from trying to invoke a remote service or access a shared resource if this operation is highly likely to fail.  

**When not to use this pattern ?**  
Do not use this :  
* For handling access to local private resources in an application, such as in-memory data structure. In this environment, using a circuit breaker would add overhead to your system.  
* As a substitute for handling exceptions in the business logic of your applications.  

**How it is different from Retry pattern ?**  
The Retry pattern enables an application to retry an operation in the expectation that it'll succeed.     
However , Circuit Breaker pattern prevents an application from performing an operation that is likely to fail.  

**Can we combine Retry pattern with Circuit Breaker pattern ?**  
YES we can. We can combine these two patterns by using the Retry pattern to invoke an operation through a circuit breaker.    
However, the retry logic should be sensitive to any exceptions returned by the circuit breaker and abandon retry attempts if the circuit breaker indicates that a fault is not transient  

**Is it customizable ?**  
Yes it is.  

We can customize it according to the type of the possible failure.   
For example : 
* **Exponentail backoff :** We can apply an increasing timeout timer to a circuit breaker(like in Retry with exponential backoff pattern).We could place the circuit breaker in the Open state for a few seconds initially, and then if the failure hasn't been resolved increase the timeout to a few minutes, and so on.  
* **Default Value :** In some cases, rather than the Open state returning failure and raising an exception, it could be useful to return a default value that is meaningful to the application(like Replace-by feature of Service gateway).  
* **Service Ping :** In the Open state, rather than using a timer to determine when to switch to the Half-Open state, a circuit breaker can instead periodically ping the service to determine whether it's become available again  
* **Manual Override :** We can provide a manual reset option that enables us to close a circuit breaker (and reset the failure counter). 
Similarly, we could force a circuit breaker into the Open state (and restart the timeout timer) if the operation protected by the circuit breaker is temporarily unavailable.  
* **Logging and Monitoring :** We can implement logging when the state changes. These logs can be used by Administrators to monitor health of service or network(for frequent state change)   
* **Replay Failed request :** In the Open state, rather than simply failing quickly, a circuit breaker could also record the details of each request to a log and arrange for these requests to be replayed when the service becomes available.  


Reference :
* Retry pattern  
* Resiliency Pattern  
* Strategy Pattern  
* Health Endpoint Monitoring pattern  
* Netflix Hystrix  
* Polly  

Happy Learning :)

[:house:Home](https://github.com/debbiswal/Articles) | [Back](https://github.com/debbiswal/Articles/blob/master/README.md#api-gateway)
