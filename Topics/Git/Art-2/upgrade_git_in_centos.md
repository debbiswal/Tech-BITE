[Home](https://debbiswal.github.io/Tech-BITE/) \| [Back](https://debbiswal.github.io/Tech-BITE/#git)  

## Upgrading GIT in CENTOS from source code  

In CENTOS , simply uninstalling the current git and installing new one does not works.  
It will always install the configured version (for me , it is 1.8).  
But I wanted the latest version. So the only way is to build it from source code , install it and change the environment variable to point to the newly installed location.  

**Below are the steps :**  

* Step 1 – Install Required Packages  
```shell
sudo yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel  
sudo yum install gcc perl-ExtUtils-MakeMaker  
```

* Step 2 – Install Git 2.17.1  
```shell
cd /usr/src  
wget https://www.kernel.org/pub/software/scm/git/git-2.17.1.tar.gz  
tar xzf git-2.17.1.tar.gz  

cd git-2.17.1  
make prefix=/usr/local/git all  
make prefix=/usr/local/git install  
```

Note : you can download any other version of git from https://mirrors.edge.kernel.org/pub/software/scm/git/

* Step 3 – Setup Environment  
```shell
echo "export PATH=/usr/local/git/bin:$PATH" >> /etc/bashrc  
source /etc/bashrc  
```


DONE..  
now verify the git version  
```shell
git --version
```  

Happy Learning :)  

[Home](https://debbiswal.github.io/Tech-BITE/) \| [Back](https://debbiswal.github.io/Tech-BITE/#git)  
