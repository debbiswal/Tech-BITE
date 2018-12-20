[Home](https://debbiswal.github.io/Tech-BITE/) \| [Back](https://debbiswal.github.io/Tech-BITE/#docker)  

# How to access local host from a container  

I have a Redis cluster running on local host(vagrant). The ports on which Redis is running are 8001 to 8006.  

I have created a small program  which will connect to Redis cluster . The program is containerized. The container is exposing ports 8001 to 8006.   

In order to acces the local machine port from container , we need to add “—network host”  

Example :  
```shell
docker run --network host --name myredisclientcontainer -p 8001-8006:8001-8006  -it myredisclientimage
```  

Happy Learning :)  

[Home](https://debbiswal.github.io/Tech-BITE/) \| [Back](https://debbiswal.github.io/Tech-BITE/#docker)  

