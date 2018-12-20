[Home](https://debbiswal.github.io/Articles/) \| [Back](https://debbiswal.github.io/Articles/#linux)

**How to get Linux Memory Utilization ?**  
```shell
top -o %MEM
```

OR

```shell
ps -eo size,pid,user,command --sort -size 
| awk '{ hr=$1/1024 ; printf("%13.2f Mb ",hr) } { for ( x=4 ; x<=NF ; x++ ) { printf("%s ",$x) } print "" }' 
| cut -d "" -f2 
| cut -d "-" -f1 
| more 
```  


**How to check a Linux box is running in VmWare ?**  
```shell
egrep -i 'virtual|vbox' /var/log/dmesg
```  


**How to get Host name from IP in Linux ?**  
```shell
host IP | cut -d " " -f 5 | cut -d "." -f 1
```  

[Home](https://debbiswal.github.io/Articles/) \| [Back](https://debbiswal.github.io/Articles/#linux)
