[Home](https://debbiswal.github.io/Tech-BITE/) \| [Back](https://debbiswal.github.io/Tech-BITE/#go)  
## How to disable certificate validation for https request in GO  

* Set the TLSClient config globally
```go
http.DefaultTransport.(*http.Transport).TLSClientConfig = &tls.Config{InsecureSkipVerify: true}
```  

* Now use the http object to make request
```go
req, err := http.NewRequest("GET", "https://123.12.3.125:8091/pools/nodes", nil)
```  


Happy Learning :)

[Home](https://debbiswal.github.io/Tech-BITE/) \| [Back](https://debbiswal.github.io/Tech-BITE/#go)  
