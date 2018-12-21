[Home](https://debbiswal.github.io/Tech-BITE/) \| [Back](https://debbiswal.github.io/Tech-BITE/#gatling)  

## Executing different scenarios dynamically in Gatling  

**Requirement**  
I have an api 'myOrderAPI' running on local port 10001.  

I want to do a load test with different scenarios like :  
* Rampup 10 users in 5 seconds  
* next , Do nothing for 20 seconds  
* next , Rampup 10 users in 5 seconds  
* next , Do nothing for 10 seconds  

**Prerequisite :**  
Copy the attached [org.json.jar.zip](https://github.com/debbiswal/Tech-BITE/raw/master/Topics/Gatling/Art-1/org.json.jar.zip) file to lib folder of gatling application , and unzip it.  
OR  
You can download it from http://www.java2s.com/Code/Jar/o/Downloadorgjsonjar.htm  


**JSON**  
Create scenarios.json in data folder , which holds the scenario details :  
<script src="https://gist.github.com/debbiswal/7bf103f727ee3a9cdd320c60ff9b99cc.js?file=scenarios.json"></script>

In the above example :  
* scenario : is the scenario name  
* url : is the url part which will be used with baseurl  
* type = Injection type  
* args=Arguments to Injection type  

Like :  
* nothingFor : is used to make steady state for 20 seconds  
* rampUsers : is used for ramping up 10 users  in 5 seconds  

for more settings  , check *getInjectionStep* method in CustomSimulation.scala.  

The other options available are :  
* atOnceUsers :  load N number of users at a time  
* constantUsersPerSec : Per second , load N number of user at a time  
* rampUsersPerSec : Per second , ramp N number of users  

As the above JSON is an array , we can implement multiple scenarios with custom url and injection pattern  

**Gatling script :**  
CustomSimulation.scala  
<script src="https://gist.github.com/debbiswal/7bf103f727ee3a9cdd320c60ff9b99cc.js?file=CustomSimulation.scala"></script>

*Note : You need to change the baseurl and maxDuration as per your need.  
If you are changing the package name and class name in CustomSimulation.scala then , remember to use same in command line.*

**Command**  
```bash
./gatling.sh -s Order.CustomSimulation
```  


Happy Learning :) 

[Home](https://debbiswal.github.io/Tech-BITE/) \| [Back](https://debbiswal.github.io/Tech-BITE/#gatling)  
