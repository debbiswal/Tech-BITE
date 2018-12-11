**How to get Linux Memory Utilization ?**  
```top -o %MEM```

OR

```ps -eo size,pid,user,command --sort -size | awk '{ hr=$1/1024 ; printf("%13.2f Mb ",hr) } { for ( x=4 ; x<=NF ; x++ ) { printf("%s ",$x) } print "" }' |cut -d "" -f2 | cut -d "-" -f1 | more```

**How to check a Linux box is running in VmWare ?**  
```egrep -i 'virtual|vbox' /var/log/dmesg```  

**How to get Host name from IP in Linux ?**  
```Get host name from IP in Linux```  
