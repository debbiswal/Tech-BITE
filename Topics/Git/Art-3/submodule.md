[Home](https://debbiswal.github.io/Tech-BITE/) \| [Back](https://debbiswal.github.io/Tech-BITE/#git)  

## Submodules - I {DRAFT VERSION}  

What is a Submodule?  
A submodule is an external git repository , which we refer and use in our repository as a sub-repository.  

In simple terms , git submodules are shared libraries or plugins , which we use in our project , by refering them  to reuse the existing code.  

These sub modules exists as an independent repo within our main(parent) repo.  
As submodules are independent repo on its own ,we also can modify and commit changes to these submodules.  

Lets start with an example :  
Say , I have a repository *Customer* and *Product*.  
Also , I have a repo *TextFileLogger* and *DBLogger*.  

Now , I want to reuse the *TextFileLogger* in my *Customer* and  *Product* repo.  
For this , I can copy the contents of *TextFileLogger* and paste it inside my *Customer* and  *Product* repo.  

But , with this approach I will not be able to track the changes made to *TextFileLogger*.  
Every time , I need to check for updates happning on *TextFileLogger* and copy paste them in my *Customer* and  *Product* repo , which is cumbersome.  

So in summary , the problems with this approach are:  

* The original reference is lost. When we copy and paste code, there’s no reference back to the original spot where the code was found .  

* Updates aren’t easily integrated. When changes are made to the original code we copied, it becomes very hard to track what’s changed so we can apply those changes back to our cut and pasted code. Some third party libraries can have thousands of lines of code, spread across hundreds of files, and it’s impossible to keep things synchronized manually.  

* Version information isn’t maintained. When we copy and paste code, there’s no easy way to know we are using version 1.0.0 of library *TextFileLogger*  and how will we remember to update our code when version 1.0.1 is released?

The simplest solution , is to refer *TextFileLogger* repo from our repo. And pull/fetch the *TextFileLogger* repo when-ever required.

Now , say there are some changes happend to *TextFileLogger* :  
* change-1 : a new file LOGGING_V1.txt is added with commit id - COMMIT-ID-01
* change-2 : a new file LOGGING_V2.txt is added with commit id - COMMIT-ID-02

We will upgrade the *TextFileLogger* in *Product* repo , to COMMIT-ID-02.  
But *Customer* repo will only be able to use the COMMIT-ID-01 , due to some backward compatibility of other modules inside it.  

This kind of facility makes use of submodules simpler , as we can refer to different commit state in our different projects/repositories.  

![repo](images/img1.png)  

Lets start with creating the required repositories :  

Create TextFileLogger repo with some dummy content :  
```shell
# Create a Github remote repository from CLI
curl -u 'debbiswal' https://api.github.com/user/repos -d "{\"name\":\"TextFileLogger\"}"

#Create the local repo TextFileLogger
mkdir TextFileLogger
cd TextFileLogger
git init
echo "TextLogger_V0" > TextLogger_V0.txt
git add TextLogger_V0.txt
git commit -m "added TextLogger_V0.txt"

#Now link the local repo with remote
git remote add origin https://github.com/debbiswal/TextFileLogger.git

# Check the remote links
git remote -v
Output :
origin	https://github.com/debbiswal/TextFileLogger.git (fetch)
origin	https://github.com/debbiswal/TextFileLogger.git (push)


#Now push the local repo data to remote
git push -u origin master

# For subsequent push , we dont have to use the' -u origin master' argument
echo "TextLogger Updated-1" > TextLogger_V0.txt

git add TextLogger_V0.txt
git commit -m "added TextLogger_V0.txt"
OR
git commit -am "added TextLogger_V0.txt"

git push
```  
In the similar way lets create the below repositories :  
* DBogger  with a fie DBLogger_V0.txt
* Customer with a file Customer_V0.txt
* Product with a file Product_V0.txt

So Now we have all our repositories ready.
Lets use the TextFileLogger repo in Customer and Product repos and submodule.  

### Adding Submodules
Lets add the *TextFileLogger* as submodule to *Customer* repo
```bash
# Get into Customer repo folder
$ cd Customer

# Add the TextFileLogger as a submodule
[Customer]$ git submodule add https://github.com/debbiswal/TextFileLogger.git
# Output
Cloning into 'Customer/TextFileLogger'...
remote: Enumerating objects: 3, done.
remote: Counting objects: 100% (3/3), done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), done.
```  

Now , if we check the Customer folder .. we can see that TextFileLogger folder is created with all the contents.  
```bash
[Customer]$ ls
Customer_V0.txt  TextFileLogger

[Customer]$ ls TextFileLogger
Logger_V0.txt

# If tree command is not installed , then you have to install it.
# As my ststem is CENTOS , I have used the command 'sudo yum -y install tree'
[Customer]$ tree
.
├── Customer_V0.txt
└── TextFileLogger
    └── Logger_V0.txt

```

Adding submodule , added some settings in our local configuration of *Customer* repo:  
Lets check the *config* file under *.git* folder.  
```bash
[Customer]$ cat .git/config
[core]
	repositoryformatversion = 0
	filemode = true
	bare = false
	logallrefupdates = true
[remote "origin"]
	url = https://github.com/debbiswal/Customer.git
	fetch = +refs/heads/*:refs/remotes/origin/*
[branch "master"]
	remote = origin
	merge = refs/heads/master
[submodule "TextFileLogger"]
	url = https://github.com/debbiswal/TextFileLogger.git
	active = true
```
We can see that a **'[submodule "TextFileLogger"]'** section has been added to .git/config file.  

Now , lets check the status of our *Customer* repo.
```bash
[Customer]$ git status
On branch master
Your branch is up to date with 'origin/master'.

Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	new file:   .gitmodules
	new file:   TextFileLogger
```  
We can see that , it also staged two files (.gitmodules , TextFileLogger)  

But what is this **.gitmodules** file ?  
Lets print the contents of *.gitmodules* file from *Customer* repo :  
```bash
[Customer]$ cat .gitmodules 
[submodule "TextFileLogger"]
	path = TextFileLogger
	url = https://github.com/debbiswal/TextFileLogger.git
```

This is similar to **'[submodule "TextFileLogger"]'** section in .git/config file .  

**So why the duplication ?**  
Its because our local config(.git/config) resides on our local machine .  

And other contributors to *Customer* repo does not have any clue that which submodule has been added .  
So , they need some information about , which submodules they have to setup in their own repo(after cloning Customer repo on their system).   

This is what .gitmodules is for.

We will see verry soon that how other contributors will use this *.gitsubmodules* file , to setup sobmodules in their repo.  

Now , lets come back to our *Customer* repo.  
If you have noticed **git status** command output , we saw that two files (.gitsubmodules,TextFileLogger) has been added.
But , *TextFileLogger* repo , is added to our *Customer* repo as subrepo.
So any changes made to *TextFileLogger* repo should be visible to us.
But we did not see , any information related to *TextFileLogger* repo.

So , how do we see those information about chnages made to subrepos?
Status, like logs and diffs, is limited to the active repo , not to submodules, which are nested repos. 
So we need to set up a submodule-aware status for the repo OR globally:

```bash
# Check the repo level submoduleSummary config 
[Customer] $ git config status.submoduleSummary
# Output :
false

# Check the global level submoduleSummary config 
[Customer] $ git config --global status.submoduleSummary
# Output :
false

# We can set the submoduleSummary config repo level OR global level(for all repos)

# Set status.submoduleSummary to true  , repo level
[Customer] $ git config status.submoduleSummary true

# OR

# Set status.submoduleSummary to true  , global level(for all repos)
[Customer] $ git config --global status.submoduleSummary true
```

Lets try again **git status** command again :  
```bash
[Customer]$ git status
On branch master
Your branch is up to date with 'origin/master'.

Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

	new file:   .gitmodules
	new file:   TextFileLogger

Submodule changes to be committed:

* TextFileLogger 0000000...cf93a5d (1):
  > Create Logger_V0.txt
```
Now you see , we got the status related to submodule *TextFileLogger* also.  It says, that it has 1 commit made. 
The last commit was an addition(right angle bracket , >) and the last commit meesage was 'Create Logger_V0.txt' .

So , whats the status of our submodule.
Lets check :
```bash
[Customer]$ cd TextFileLogger/
[Customer/TextFileLogger]$ git status
On branch master
Your branch is up to date with 'origin/master'.

nothing to commit, working tree clean
```
In the above example , we saw that , the status is only shown for the TextFileLogger submodule repo.
Its because of a new *.git* file takes over the responsibilities.
Yes , there is a file *.git* exists in the directory.

Lets see the contents of the *.git* file :
```bash
[Customer/TextFileLogger]$ cat .git
gitdir: ../.git/modules/TextFileLogger
```  

Git does not leave submodule repo directories inside the main repo’s working directory, but keep these in the main repo's .git directory (inside .git/modules), and uses a *gitdir* reference in submodules.

Lets check the Customer/.git/modules folder :  

```bash
[Customer/TextFileLogger]$ cd ..
[Customer]$ cd .git/modules/
[Customer/.git/modules]$ ls
TextFileLogger
[Customer/.git/modules]$ cd TextFileLogger/
[Customer/.git/modules/TextFileLogger]$ ls
branches  config  description  HEAD  hooks  index  info  logs  objects  packed-refs  refs
```
We can see here that , there is a folder *TextFileLogger* . And it has all ncessary files & folders to represent the *TextFileLogger* repo.  

Now  , Lets push the changes :  
```
[Customer/.git/modules/TextFileLogger]$ cd ../../..
[Customer]$ git commit -m "Adding the submodule TextFileLogger"
Outpput :
[master e6a4e13] Adding the submodule TextFileLogger
 2 files changed, 4 insertions(+)
 create mode 100644 .gitmodules
 create mode 160000 TextFileLogger

[Customer]$ git push
```
Now if we see the repository in github website , we can see that the *TextFileLogger* is saved as a reference with specific commit id.  
There is no physical folder exists for *TextFileLogger* inside *Customer* repo.  
See the below pic :  
![Customer](images/img2.PNG)  

If we click the *TextFileLogger* link in *Customer* repo , we will be redirected to *TextFileLogger* repo with specific commit id.  
See the below pic :  
![TextFileLogger](images/img3.PNG)  

But do remember that , the information about submodules  , which was added to .git/config file  , never pushed to remote.  
Submodules information is only saved in .gitmodules file , which is pushed to remote.  
We will discuss these things in next topic.  

### Cloning a repo with submodule
Clone the Customer repo into a different folder
```
cat .git/config
cat .gitmodules
# initialize submodule
git submodule init
Output : ????
cat .git/config
# now we need to fetch the submodule codebase 
git submodule update
Outpput : ????

#now we have submodule codebase
ls TextFileLogger
```

Adding submodule from a specific branch or commit
show output of git status , gif diff --cache , cat .gitmodule , cat .git/configure  
How to clone a repo , issues with submodules while cloning , commands to be used , recursive  
Modifying submodule , commiting mainmodule without commiting submodule  
How to derefer a submodule  
How to refere to different version  
How to switch to a different submodule  
How to remove a submodule 
Parallelized fetching,shallow-submodules(https://dzone.com/articles/the-2016-git-retrospective-submodules)  



### Disadvantages of Submodule  
* We’ve been talking about how susceptible Git Submodules are towards inconsistencies. That seems to be almost the only problem that threatens Git submodules. Listed here are some of the scenarios this issue occur :
  * When one of the collaborators have made changes to the submodule but doesn’t push the submodule repo, other collaborators will not be in sync.
  * When one of the collaborators have made commit changes to the submodules if others don’t use the ‘git submodule update’ command. The repo will have inconsistent commits.
  * If two different branches are being merged, the submodules remain unchanged and submodule update command must be used without fail.
* It’s almost impossible to track the entire project at a time when using submodules. Changes in the submodules aren’t tracked by the main module and must change the current working directory to look at the changes.  
* It’s hard to use IDEs when using Git Submodules and developers are forced to use the shell to maintain consistency.  



Happy Learning :)  

[Home](https://debbiswal.github.io/Tech-BITE/) \| [Back](https://debbiswal.github.io/Tech-BITE/#git)  
