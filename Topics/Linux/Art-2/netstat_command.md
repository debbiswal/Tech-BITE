[Home](https://debbiswal.github.io/Tech-BITE) \| [Back](https://debbiswal.github.io/Tech-BITE/#linux)

## Find number of active connections in Linux using netstat

**How to see ALL of the connections?**  
```shell
netstat -a
```  

**How to see count of all connections that we presently have to our machine?**  
```shell
netstat -an | wc -l
```  

**How to see traffic comming across port 80 or a perticular port?**  
```shell
netstat -an | grep :80 | wc -l
```  

**How to see count of all connections based on their state/category?**  
```shell
netstat -ant | awk '{print $6}' | sort | uniq -c | sort -n
```  

Below is the example of output :  
```shell
netstat -ant | awk '{print $6}' | sort | uniq -c | sort -n
      1 CLOSING
      1 established
      1 FIN_WAIT2
      1 Foreign
      2 CLOSE_WAIT
      6 FIN_WAIT1
      7 LAST_ACK
      7 SYN_RECV
     37 ESTABLISHED
     44 LISTEN
    297 TIME_WAIT
```  

**How to see network statitstics?**  
```shell
netstat -s
```  

**How to see the process(PID) which has the connection?**  
```shell
netstat -p
```


[Home](https://debbiswal.github.io/Tech-BITE) \| [Back](https://debbiswal.github.io/Tech-BITE/#linux)
