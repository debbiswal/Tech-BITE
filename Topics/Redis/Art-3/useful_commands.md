[Home](https://debbiswal.github.io/Tech-BITE) \| [Back](https://debbiswal.github.io/Tech-BITE/#redis)

## Useful Commands  

* How to get StackExchange.Redis client list for a Redis node   
```bash
./redis-cli -h {HOST} -p {PORT} client list | grep SE_RedisClient | awk '{print $2}'|cut -d: -f1 | sort | uniq -c
```  


Happy Learning :)  

[Home](https://debbiswal.github.io/Tech-BITE) \| [Back](https://debbiswal.github.io/Tech-BITE/#redis)
