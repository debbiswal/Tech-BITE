[:house:Home](https://github.com/debbiswal/Articles)  

**List all PODS with Containers | Kubernetes**
```
kubectl get pods --all-namespaces -o=jsonpath='{range .items[*]}{"\n"}{.metadata.name}{":\t"}{range .spec.containers[*]}{.image}{", "}{end}{end}' |sort  
```  


Happy Learning :smiley:  

[:house:Home](https://github.com/debbiswal/Articles)
