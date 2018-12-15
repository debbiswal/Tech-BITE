[:house:Home](https://github.com/debbiswal/Articles)  

## How to copy files to multiple servers using WinSCP 

Lets say you want to copy a file log.txt from D drive to /tmp folder of myredishost.dotcom.org Linux server.  
You can do it by using WinSCP application.  

Only problem with WinSCP is , if you want to copy files to multiple server , you have to login to every server using WinSCP and copy the files.  

Is there any simple solution for this?  

Yes … it is there.  
We can use WinSCP scripting to achieve this task.  

Here HOW it is.  

1)	Go to the folder where winscp.exe file is present.  

2)	Create a file copyScript.txt  

3)	Copy paste the below code  
```
open sftp://USERID:"PASSWORD"@HOSTNAME/ -hostkey=*
lcd D:
cd /tmp
put D:\log.txt
exit
```

4)	Modify the below things :
a.	USERID
b.	PASSWORD – put the password in double quotes so that you can provide ‘@’ character , which will not mismatch with ‘@’ character before HOSTNAME
c.	HOSTNAME – provide the full DNS like : myhost.org
d.	Change the put command to provide the file/folder you want to copy. Here I have kept D:\log.txt file to copy

5)	Save the file  

6)	In command prompt type the below thing :  
```
WinSCP.exe /log="D:\WinSCP.log" /ini=nul /script=copyScript.txt
```  
The above command will execute the script copyScript.txt and create the log file WinSCP.log in D drive.  
You can change the path of the log file as needed.  

7)	Once the command is executed , verify the /tmp folder on the required Linux server  

8)	If the files are not copied , then check the WinSCP.log file for details.  

You can copy folders , by simple replacing file name with folder name .   
It will copy the full folder with subfolder details.  

You can also modify the script to copy the file to multiple servers.  

Happy Learning :smiley:  

[:house:Home](https://github.com/debbiswal/Articles)
