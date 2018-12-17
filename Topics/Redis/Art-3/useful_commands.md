[:house:Home](https://github.com/debbiswal/Articles)  

## Useful Commands  

* How to get StackExchange.Redis client list for a Redis node   
```bash
./redis-cli -h {HOST} -p {PORT} client list | grep SE_RedisClient | awk '{print $2}'|cut -d: -f1 | sort | uniq -c
```  


Happy Learning :smiley:  

[:house:Home](https://github.com/debbiswal/Articles)
