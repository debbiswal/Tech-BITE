[:house:Home](https://github.com/debbiswal/Articles) | [Back](https://github.com/debbiswal/Articles/blob/master/README.md#aws)  

# How to copy a file from local machine to AWS EC2 instance  

**Syntax:**  
```shell
scp -i "<pem file name>"  <file to copy with path>  <user>@<EC2 instance URL>:<Destination path on EC2 instance>
```  

**Example :**  
```shell
scp -i "my_aws.pem"  testcopy.txt ec2-user@ec2-12-345-67-890.eu-west-1.compute.amazonaws.com:/home/ec2-user/testfolder.
```  

Happy Learning :smiley:  

[:house:Home](https://github.com/debbiswal/Articles) | [Back](https://github.com/debbiswal/Articles/blob/master/README.md#aws)  
