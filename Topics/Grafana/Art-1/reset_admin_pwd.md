[:house:Home](https://github.com/debbiswal/Articles) | [Back](https://github.com/debbiswal/Articles/blob/master/README.md#grafana)

**Resetting lost Admin password :**  

Connect to the Grafana pod in kubernetes which is running in namespace ‘monitoring’  
```kubectl exec -it --namespace=monitoring {grafana_pod_name} /bin/sh  ```  

Change the password in the pod  
```grafana-cli admin reset-admin-password --homepath "/usr/share/grafana" {new_password}```   
    
    
    
    
**Changing Admin password (did not lost) :**

If you only want to change the password(did not lost admin password)  :  
```
curl -X PUT -H "Content-Type: application/json" -d '{
  "oldPassword": "current_password",
  "newPassword": "new_password",
  "confirmNew": "new_password"
}' http://admin:{current_password}@<your_grafana_host>:3000/api/user/password
```  

Happy Learning :smiley:  

[:house:Home](https://github.com/debbiswal/Articles) | [Back](https://github.com/debbiswal/Articles/blob/master/README.md#grafana)
