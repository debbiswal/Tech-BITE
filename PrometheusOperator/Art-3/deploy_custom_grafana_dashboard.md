[:house:Home](https://github.com/debbiswal/Articles)

# How to deploy custom Grafana dashboard as part of Grafana Deployment in Prometheus Operator

The purpose of this article is to show , How to deploy custom Grafana dashboard as part of Grafana Deployment in Prometheus Operator.
So that , it will be available with the deployment and  You don’t have to create it  , after deploying Grafana.  

Here I am using a Dashboard , which I have created to monitor Persistent Volumes in Kubernetes  

* Open the required dashboard and click on ‘Share’ link from tool bar :  
![toolbar][images/img1.png]  

* Go to ‘Export’ tab  
![toolbar][images/img1.png]  
* Click on ‘View JSON’ button from ‘Export’ tab  
![toolbar][images/img1.png]  
* Scroll down and remove the ‘uid’ entry from JSON . It will be automatically created when deployed.  
![toolbar][images/img1.png]  

Happy Learning :smiley:
[:house:Home](https://github.com/debbiswal/Articles)
