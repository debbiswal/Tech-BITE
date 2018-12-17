[:house:Home](https://github.com/debbiswal/Articles)  
## How to use DOCKER commands without SUDO  

Below are the steps needed to get rid of using sudo commands while working in DOCKER.  

Start the docker daemon(if not stared)  
```bash
$ sudo service docker start
```  

Create the docker group.  
```bash
$ sudo groupadd docker
```  

Add your user to the docker group.  
```bash
$ sudo usermod -aG docker $USER
```  

Log out and log back in so that your group membership is re-evaluated(if you are using Vagrant , then restart the vagrant instance).  

If testing on a virtual machine, it may be necessary to restart the virtual machine for changes to take effect.  
On a desktop Linux environment such as X Windows, log out of your session completely and then log back in.  

Verify that you can run docker commands without sudo.  

**Till this step it solved my issue**  

*Note : If you initially ran Docker CLI commands using sudo before adding your user to the docker group, you may see the following error, which indicates that your ~/.docker/ directory was created with incorrect permissions due to the sudo commands.*  

**WARNING: Error loading config file: /home/user/.docker/config.json -  
stat /home/user/.docker/config.json: permission denied**  

To fix this problem, either remove the ~/.docker/ directory (it is recreated automatically, but any custom settings are lost), or change its ownership and permissions using the following commands:  
```shell
$ sudo chown "$USER":"$USER" /home/"$USER"/.docker -R
$ sudo chmod g+rwx "/home/$USER/.docker" –R
```  

Happy Learning :smiley:  

[:house:Home](https://github.com/debbiswal/Articles)
