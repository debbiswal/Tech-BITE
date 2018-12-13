[:house:Home](https://github.com/debbiswal/Articles)

# Deploying Prometheus StatefulSet with Persistent Volume in Prometheus-Operator  

Prometheus-Operator deploys Prometheus pods as Statefulset with 2 replicas .  
But the data of Prometheus pod resides inside the pod , as it uses local storage.  

I was trying to assign Persistent Volume to Prometheus pods .  
And I tried the below storage spec  to start with(using default storage provisioning) :  
[default](images/img1.png)  

But , after deploying the Prometheus-Operator , I found that the StatefulSet(prometheus-k8s) and the pods (prometheus-k8s-0, prometheus-k8s-1) are not created.  

There is no error message displayed on console , while deployment. Neither any Prometheus related pod was created and in crashing state.  

I tried to check the logs and events , but did not find anything.  

As , the deployment is of kind ‘Prometheus’ , which is a Custom Resource , I was suspecting the ‘metadata’ I have given.  
It might be a case , where the CRD is not expecting ‘name’ , and the deployment is failing.  

So I tried with , removing the ‘metadata’ section (using default storage provisioning) :  
[metadata](images/img2.png)  

And deployed the Prometheus-Operator again.  

AND.. It worked…. All  Pods,Statefulsets are UP and RUNNING :)  

It will create 2 PVC and related PV .  
The PVC are created with certain naming format like : prometheus-k8s-db-prometheus-k8s-0 , prometheus-k8s-db-prometheus-k8s-1.  

**Sample screen shot of a PVC :**  
[pvc](images/img3.png)  

**Sample screen shot of a PV :**  
[pv](images/img4.png)  

Happy Learning :smiley:  

[:house:Home](https://github.com/debbiswal/Articles)
