[:house:Home](https://github.com/debbiswal/Articles) | [Back](https://github.com/debbiswal/Articles/blob/master/README.md#docker)

### Flamegraph in Docker with sample NodeJs app  

Here is a sample container to generate Flamegraph in docker.  
Please find the attached [node-perf-master zip file](https://github.com/debbiswal/Articles/raw/master/Docker/Art-4/node-perf-master.zip) with [node-flamegraph SVG file](https://github.com/debbiswal/Articles/raw/master/Docker/Art-4/node-flamegraph.svg)(open the svg file in browser to drilldown details).  

The zip file contains the Dockerfile , a server.js file(sample node application),scripts/start.sh  

*Note : The docker image size is around 2.8GB , as it downloads the code from git and builds it there and then. It can be optimized by using prebuilt files(I hope).*  

**Steps :**  

* Step1 - Unzip the file node-perf-master.zip   
```
$ cd node-perf-master
```  

* Step2 - Build Docker Image  
```
$ docker build -t my-nodejs-app .
```  

* Step3 - Run Docker container & enable *SYS_ADMIN* privileges to run perf (Docker Console-1)  
You can visit for more info : https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities  
```
$ docker run --cap-add=SYS_ADMIN -i -t -p 8090:8080 my-nodejs-app
```  

* Step4 - Get the docker image id(running one)  
```
$docker ps
```  
Note down the image id from the result of above command  

* Step5 - Create a new Bash session in the container . Here the value *fe4f5336a048*  is the container id (Docker Console-2)  
```
$ docker exec -it fe4f5336a048 /bin/bash
```  

* Step6 - Now you are in my-nodejs-app container(Docker Console-2)  
Run a command and record its profile into perf.data for 30 seconds -p to record events on existing process id -F 99 profile at this frequency  
```
root@fe4f5336a048:/# sudo perf record -F 99 -p `pgrep -n node` -g -- sleep 30
```  
The above command when completes creates a file perf.data.

* Step7 - Create a new Bash session in the container . Here the value *fe4f5336a048*  is the container id (Docker Console-3)  
```
$ docker exec -it fe4f5336a048 /bin/bash
```  

* Step8 - Run curl command few times , so that  data will be captured by perf process. (Docker Console-3)
```
root@fe4f5336a048:/# curl http://127.0.0.1:8080/
```  

* Step9 - Generate nodestacks file(Docker Console-2)
```
root@fe4f5336a048:/# perf script > nodestacks
```  

* Step10 - Make nodestacks file human readable(Docker Console-2)
```
root@fe4f5336a048:/#  sed -i '/\[unknown\]/d' nodestacks
```  

* Step11 - Generate FlameGraph(Docker Console-2)
```
root@fe4f5336a048:/# git clone --depth 1 http://github.com/brendangregg/FlameGraph.git
root@fe4f5336a048:/#  cd FlameGraph
root@fe4f5336a048:/# ./stackcollapse-perf.pl < ../nodestacks | ./flamegraph.pl --colors js > ../node-flamegraph.svg
```  

* Step12 - From vagrant console Copy the file from docker container to /home/vagrant
```
$ docker cp fe4f5336a048:/node-flamegraph.svg  /home/vagrant/
```  

Open the node-flamegraph.svg in browser. Click on the required blocks to drill down.  

Screenshot :  
![flamegraph](node-flamegraph.svg)  

Happy Learning :smiley:  

[:house:Home](https://github.com/debbiswal/Articles) | [Back](https://github.com/debbiswal/Articles/blob/master/README.md#docker)
