[Home](https://debbiswal.github.io/Articles/) \| [Back](https://debbiswal.github.io/Articles/#redis)

## Useful Commands  

* How to get StackExchange.Redis client list for a Redis node   
```bash
./redis-cli -h {HOST} -p {PORT} client list | grep SE_RedisClient | awk '{print $2}'|cut -d: -f1 | sort | uniq -c
```  


Happy Learning :)  

[Home](https://debbiswal.github.io/Articles/) \| [Back](https://debbiswal.github.io/Articles/#redis)
