[:house:Home](https://github.com/debbiswal/Articles)

# How to run remote AWS session in background  

Syntax:  
```shell
ssh -f -i "<pem file name>"  <user>@<EC2 instance URL>:<file with path from EC2 instance> “bash <script file with path on EC2>”
```  

Example :
```shell
ssh -f -i "my_aws.pem"  ec2-user@ec2-12-345-67-890.eu-west-1.compute.amazonaws.com  “bash /home/ec-2user/myscripts/run.sh ”
```  

When you run the above command , the prompt will immediately come back , but the result of the executed command will be printed on your screen after some time(when the executed command finished).  

If you want to avoid that , redirect the output to NULL device. OR you can redirect the output to a file also.  

```shell
ssh -f -i "my_aws.pem"  ec2-user@ec2-12-345-67-890.eu-west-1.compute.amazonaws.com  “bash /home/ec-2user/myscripts/run.sh > /dev/null 2>&1”
```  

Happy Learning :smiley:  

[:house:Home](https://github.com/debbiswal/Articles)
