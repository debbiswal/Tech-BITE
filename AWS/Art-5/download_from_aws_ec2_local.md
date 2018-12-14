[:house:Home](https://github.com/debbiswal/Articles)  

# How to download files from AWS EC2 to local machine  

**Syntax:**
```
scp -i "<pem file name>"  <user>@<EC2 instance URL>:<file with path from EC2 instance> <destination folder on local machine>
```  

**Example :**
```
scp -i "my_aws.pem"  ec2-user@ec2-12-345-67-890.eu-west-1.compute.amazonaws.com:/home/ec2-user/testfolder/testcopy.txt  /home/vagrant/aws_dowloads
```  


Happy Learning :smiley:  

[:house:Home](https://github.com/debbiswal/Articles)
