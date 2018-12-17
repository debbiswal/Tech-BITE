[:house:Home](https://github.com/debbiswal/Articles) | [Back](https://github.com/debbiswal/Articles/blob/master/README.md#aws)  

# How to run a shell script on AWS EC2 from local machine  

**Syntax:**  
```shell
ssh -i "<pem file name>"  <user>@<EC2 instance URL>:<file with path from EC2 instance> “bash <script file with path on EC2>”
```  

**Example :**  
```shell
ssh -i "my_aws.pem"  ec2-user@ec2-12-345-67-890.eu-west-1.compute.amazonaws.com  “bash /home/ec-2user/myscripts/run.sh”
```  

Happy Learning :smiley:  

[:house:Home](https://github.com/debbiswal/Articles) | [Back](https://github.com/debbiswal/Articles/blob/master/README.md#aws)  
