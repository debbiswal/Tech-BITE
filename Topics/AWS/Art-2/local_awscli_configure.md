[Home](https://debbiswal.github.io/Tech-BITE/) \| [Back](https://debbiswal.github.io/Tech-BITE/#aws)  

## How to configure AWS Cli on local machine with Auto complete feature

**Installing AWSCli**  
```shell
sudo yum -y install python-pip    =>  It will install pip python package . pip is required to install other python packages
pip install awscli --upgrade --user   =>  Install awscli
ls ~/.local/bin => you will be able to see aws file
```  

**Configuring path**  
Edit user profle to add awscli path :  
```shell
vi ~/.bash_profile  
export PATH=~/.local/bin:$PATH  
source ~/.bash_profile   => this command will load the modified profile to current session. Else you have to relogin to take effect the changes  
```  

How to make sure awscli is installed ?  
type ‘aws --v’  

**Configure AWS Cli**  
Type command ‘awsconfig’   
Provide the accesskey , scretkey , region and output format(JSON)  

These details are saved in config and credentials file in ~/.aws folder of local machine.  
 
Configuring Command completion(auto complete)  
This step is optional. But it is helpful while using command line.  
For auto completion , you can press TAB key.  
```shell
vi ~/.bashrc  
complete -C '~/.local/bin/aws_completer' aws => add this line to file  

vi ~/.bash_profile  
source ~/.bashrc  
```  

*Note : I have Cent OS  , so is your OS is different , then yum command will not work. Use the respective command.*

Happy Learning :smiley:  

[Home](https://debbiswal.github.io/Tech-BITE/) \| [Back](https://debbiswal.github.io/Tech-BITE/#aws)  
