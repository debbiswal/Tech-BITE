[Home](https://debbiswal.github.io/Tech-BITE/) \| [Back](https://debbiswal.github.io/Tech-BITE/#git)  

## Git Submodules - I 

### What is a Submodule ?
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
<pre class="highlight"><code><span class="c"># Create a Github remote repository from CLI</span>
$ curl -u 'git-user' https://api.github.com/user/repos -d "{\"name\":\"TextFileLogger\"}"

<span class="c">#Create the local repo TextFileLogger</span>
$ mkdir TextFileLogger
$ cd TextFileLogger
[TextFileLogger]$ git init
[TextFileLogger]$ echo "TextLogger_V0" > TextLogger_V0.txt
[TextFileLogger]$ git add TextLogger_V0.txt
[TextFileLogger]$ git commit -m "added TextLogger_V0.txt"

<span class="c">#Now link the local repo with remote</span>
[TextFileLogger]$ git remote add origin https://github.com/git-user/TextFileLogger.git

<span class="c"># Check the remote links</span>
[TextFileLogger]$ git remote -v
Output :
<span style="color:#c5860b">origin	https://github.com/git-user/TextFileLogger.git (fetch)</span>
<span style="color:#c5860b">origin	https://github.com/git-user/TextFileLogger.git (push)</span>


<span class="c">#Now push the local repo data to remote</span>
[TextFileLogger]$ git push -u origin master

<span class="c"># For subsequent push , we dont have to use the' -u origin master' argument</span>
[TextFileLogger]$ echo "TextLogger Updated-1" > TextLogger_V0.txt

[TextFileLogger]$ git add TextLogger_V0.txt
[TextFileLogger]$ git commit -m "added TextLogger_V0.txt"
OR
[TextFileLogger]$ git commit -am "added TextLogger_V0.txt"

[TextFileLogger]$ git push</code></pre>

In the similar way lets create the below repositories :  
* DBogger  with a fie DBLogger_V0.txt
* Customer with a file Customer_V0.txt
* Product with a file Product_V0.txt

So Now we have all our repositories ready.
Lets use the TextFileLogger repo in Customer and Product repos and submodule.  

### Adding Submodules
Lets add the *TextFileLogger* as submodule to *Customer* repo
<pre class="highlight"><code><span class="c"># Get into Customer repo folder</span>
$ cd Customer

<span class="c"># Add the TextFileLogger as a submodule</span>
[Customer]$ git submodule add https://github.com/git-user/TextFileLogger.git
Output :
Cloning into 'Customer/TextFileLogger'...
...
Unpacking objects: 100% (3/3), done.</code></pre>

Now , if we check the Customer folder .. we can see that TextFileLogger folder is created with all the contents.  
<pre class="highlight"><code>[Customer]$ ls
Output :
Customer_V0.txt  TextFileLogger

[Customer]$ ls TextFileLogger
Output :
Logger_V0.txt

<span class="c"># If tree command is not installed , then you have to install it.</span>
<span class="c"># As my ststem is CENTOS , I have used the command 'sudo yum -y install tree'</span>
[Customer]$ tree
Output :
.
├── Customer_V0.txt
└── TextFileLogger
    └── Logger_V0.txt</code></pre>

When we added the submodule , it utomatically added some settings in our local configuration of *Customer* repo:  
Lets check the *config* file under *.git* folder.  
<pre class="highlight"><code>[Customer]$ cat .git/config
Output :
[core]
	repositoryformatversion = 0
	filemode = true
	bare = false
	logallrefupdates = true
[remote "origin"]
	url = https://github.com/git-user/Customer.git
	fetch = +refs/heads/*:refs/remotes/origin/*
[branch "master"]
	remote = origin
	merge = refs/heads/master
<span style="color:#c5860b">[submodule "TextFileLogger"]
	url = https://github.com/git-user/TextFileLogger.git
	active = true</span></code></pre>
	
We can see that a **'[submodule "TextFileLogger"]'** section has been added to .git/config file.  

Now , lets check the status of our *Customer* repo.
<pre class="highlight"><code>[Customer]$ git status
Output :
On branch master
Your branch is up to date with 'origin/master'.

Changes to be committed:
(use "git reset HEAD file..." to unstage)<span style="color:#c5860b">
	new file:   .gitmodules
	new file:   TextFileLogger</span></code></pre>  
We can see that , it also staged two files (.gitmodules , TextFileLogger)  

But what is this **.gitmodules** file ?  
Lets print the contents of *.gitmodules* file from *Customer* repo :  
<pre class="highlight"><code>[Customer]$ cat .gitmodules 
Output :
<span style="color:#c5860b">[submodule "TextFileLogger"]
	path = TextFileLogger
	url = https://github.com/git-user/TextFileLogger.git</span></code></pre>

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

<pre class="highlight"><code><span class="c"># Check the repo level submoduleSummary config</span> 
[Customer] $ git config status.submoduleSummary
Output :
false

<span class="c"># Check the global level submoduleSummary config</span> 
[Customer] $ git config --global status.submoduleSummary
Output :
false

<span class="c"># We can set the submoduleSummary config repo level OR global level(for all repos)</span>

<span class="c"># Set status.submoduleSummary to true  , repo level</span>
[Customer] $ git config status.submoduleSummary true

<span class="c">OR</span>

<span class="c"># Set status.submoduleSummary to true  , global level(for all repos)</span>
[Customer] $ git config --global status.submoduleSummary true</code></pre>

Lets try again **git status** command again :  
<pre class="highlight"><code>[Customer]$ git status
Output :
On branch master
Your branch is up to date with 'origin/master'.

Changes to be committed:
(use "git reset HEAD file..." to unstage)<span style="color:#c5860b">
	new file:   .gitmodules
	new file:   TextFileLogger</span>

Submodule changes to be committed:

* TextFileLogger 0000000...cf93a5d (1):
  <span style="color:green">> Create Logger_V0.txt</span></code></pre>
  
We can see from the above *git status* command output that , we got the status related to submodule *TextFileLogger* also.  It says, that it has 1 commit made. 
The last commit was an addition(right angle bracket , >) and the last commit meesage was 'Create Logger_V0.txt' .

So , whats the status of our submodule.
Lets check :
<pre class="highlight"><code>[Customer]$ cd TextFileLogger/
[Customer/TextFileLogger]$ git status
Output :
On branch master
Your branch is up to date with 'origin/master'.

nothing to commit, working tree clean</code></pre>

In the above example , we saw that , the status is only shown for the TextFileLogger submodule repo.
Its because of a new *.git* file takes over the responsibilities.
Yes , there is a file *.git* exists in the directory.

Lets see the contents of the *.git* file :
<pre class="highlight"><code>[Customer/TextFileLogger]$ cat .git
Output :
<span style="color:#c5860b">gitdir: ../.git/modules/TextFileLogger</span></code></pre>  

From the above output , we found that '.git' file of *TextFileLogger* submodule only contains a reference to a folder of parent repo.
Here the reference is assigned to *gitdir*. And the path is actually *Customer/.git/modules/TextFileLogger*.

**Why is this so?**
Its because , whenever we add a submodule , GIT does not keep the meta information for the submodule inside the submodule folder.
But keep these in the main repo's .git directory (inside .git/modules), and uses a *gitdir* reference in submodules.

So that , the submodule folder will not behave as a standalone repo

Lets check the Customer/.git/modules folder :  

<pre class="highlight"><code>[Customer/TextFileLogger]$ cd ..
[Customer]$ cd .git/modules/
[Customer/.git/modules]$ ls
Output :
TextFileLogger
[Customer/.git/modules]$ cd TextFileLogger/
[Customer/.git/modules/TextFileLogger]$ ls
Output :
<span style="color:#c5860b">branches  config  description  HEAD  hooks  index  info  logs  objects  packed-refs  refs</span></code></pre>

We can see here that , there is a folder *TextFileLogger* . And it has all necessary files & folders to represent the *TextFileLogger* repo.  

Now  , Lets push the changes :  
<pre class="highlight"><code>[Customer/.git/modules/TextFileLogger]$ cd ../../..
[Customer]$ git commit -m "Adding the submodule TextFileLogger"
Outpput :
[master e6a4e13] Adding the submodule TextFileLogger
 2 files changed, 4 insertions(+)
 create mode 100644 .gitmodules
 create mode 160000 TextFileLogger

[Customer]$ git push</code></pre>

Now if we see the repository in github website , we can see that the *TextFileLogger* is saved as a reference with specific commit id.  
There is no physical folder exists for *TextFileLogger* inside *Customer* repo.  

See the below pic :  
![Customer](images/img2.PNG)  

If we click the *TextFileLogger* link in *Customer* repo , we will be redirected to *TextFileLogger* repo with specific commit id.  

See the below pic :  
![TextFileLogger](images/img3.PNG)  

But do remember that , the information about submodules  , which was added to .git/config file  , never pushed to remote.  

Submodules information is only saved in .gitmodules file , which is pushed to remote.  
We will discuss these things in next section.  

### Cloning a repo with submodule
Till now , we have created a repo and added a submodule to it.  
Now , lets try to clone the repo into a different folder , and see whether we are getting back the submodules or not.  

Clone the Customer repo into a different folder
<pre class="highlight"><code><span class="c"># Clone the Customer repo into another folder .. say CustomerClone</span>
$ git clone https://github.com/git-user/Customer.git CustomerClone
Output :
Cloning into 'CustomerClone'...
...
Unpacking objects: 100% (11/11), done.

<span class="c"># Get inside the folder</span>
$ cd CustomerClone

<span class="c"># Print the folder structure</span>
[CustomerClone]$ tree
Output :
.
├── Customer_V0.txt
└── TextFileLogger

1 directory, 1 file</code></pre>

We can see here that , only *TextFileLogger* folder is created , but its empty. There should be a file Logger_V0.txt. Which is missing.

As we can see from the output of *git clone* command , it was successfully executed , so there is no chance that clone is failed for any reason.  

So , there could be a possibility that , *Customer* repo does not have information about its submodules .
And thats why while cloning , submodules did not get cloned.

Lets verify..
<pre class="highlight"><code>[CustomerClone]$ cat .git/config
Output :
[core]
	repositoryformatversion = 0
	filemode = true
	bare = false
	logallrefupdates = true
[remote "origin"]
	url = https://github.com/git-user/Customer.git
	fetch = +refs/heads/*:refs/remotes/origin/*
[branch "master"]
	remote = origin
	merge = refs/heads/master</code></pre>
	
We can see here that , the repo *CustomerClone* does not have the submodule information in its local configuration.

So , whether .gitmodules file has the information ?
<pre class="highlight"><code>[CustomerClone]$ cat .gitmodules
Output :
<span style="color:#c5860b">[submodule "TextFileLogger"]
	path = TextFileLogger
	url = https://github.com/git-user/TextFileLogger.git</span></code></pre>
	
Yes it has.

**So ,How do we get the *TextFileLogger* submodule added to our *CustometClone* repo?**
* First we have to update the *CustomerClone* repo's local configuration with *TextFileLogger* submodule information
<pre class="highlight"><code><span class="c"># Update/Initialize submodule information in local configuration</span>
[CustomerClone]$ git submodule init
Output :
Submodule 'TextFileLogger' (https://github.com/git-user/TextFileLogger.git) registered for path 'TextFileLogger'</code></pre>

Lets verify , whether local configuration has been updated with submodule information or not :
<pre class="highlight"><code>[CustomerClone]$ cat .git/config
Output :
[core]
	repositoryformatversion = 0
	filemode = true
	bare = false
	logallrefupdates = true
[remote "origin"]
	url = https://github.com/git-user/Customer.git
	fetch = +refs/heads/*:refs/remotes/origin/*
[branch "master"]
	remote = origin
	merge = refs/heads/master
<span style="color:#c5860b">[submodule "TextFileLogger"]
	active = true
	url = https://github.com/git-user/TextFileLogger.git</span></code></pre>
	
We can see that *TextFileLogger* submodule information has been added to .git/config file.

* Update the *CustomerClone* repo from *remote* again.
<pre class="highlight"><code>[CustomerClone]$ git submodule update
Output :
Cloning into 'CustomerClone/TextFileLogger'...
Submodule path 'TextFileLogger': checked out 'cf93a5d641a1af6c558762935e8d544c90308e0e'</code></pre>

Lets check the folder structure , to see whether all files & folders are added properly
<pre class="highlight"><code>[CustomerClone]$ tree
Output :
.
├── Customer_V0.txt
└── TextFileLogger
    └── Logger_V0.txt

1 directory, 2 files</code></pre>

Yes.. now all files are added.

Lets check the *TextFileLogger* folder :  
<pre class="highlight"><code>[CustomerClone]$ cd TextFileLogger/
[CustomerClone/TextFileLogger]$ cat .git
Output:
<span style="color:#c5860b">gitdir: ../.git/modules/TextFileLogger</span></code></pre>

Yes, *TextFileLogger* folder has a .git file , which contains the reference to CustomerClone/.git/modules/TextFileLogger folder.
And this folder has the necessary meta information to make CustomerClone\TextFileLogger as a stand-alone repo on its own(like in out Customer repo).  

We can do the above two steps in a single command also :  
<pre class="highlight"><code>[CustomerClone]$ git submodule update --init
Output :
Submodule 'TextFileLogger' (https://github.com/git-user/TextFileLogger.git) registered for path 'TextFileLogger'
Cloning into 'CustomerClone/TextFileLogger'...
Submodule path 'TextFileLogger': checked out 'cf93a5d641a1af6c558762935e8d544c90308e0e'</code></pre>  

But there is a problem with this approach of adding submodules , while cloning a repo.
What if , we have nested submodules . 
We can not go into each submodule folder and run the command 'git submodule init' and 'git submodule update'.
We need a single command which will do all these for us.

Here comes *- -recursive* argument in help.
Lets test it :
<pre class="highlight"><code><span class="c"># Lets go back to the root folder</span>
[CustomerClone/TextFileLogger] $ cd ../..

<span class="c"># Delete the CustomerClone folder</span>
$ rm -rf CustomerClone

<span class="c"># Now lets clone the Customer repo to CustomerClone folder</span>
$ git clone --recursive https://github.com/git-user/Customer.git CustomerClone
Output :
Cloning into 'CustomerClone'...
...
Unpacking objects: 100% (11/11), done.
Submodule 'TextFileLogger' (https://github.com/git-user/TextFileLogger.git) registered for path 'TextFileLogger'
Cloning into 'CustomerClone/TextFileLogger'...
...
Submodule path 'TextFileLogger': checked out 'cf93a5d641a1af6c558762935e8d544c90308e0e'</code></pre>

We can see from the output of above command that , *TextFileLogger* is also cloned.

Lets verify the folder structure :
<pre class="highlight"><code>$ cd CustomerClone
[CustomerClone]$ tree
Output :
.
├── Customer_V0.txt
└── TextFileLogger
    └── Logger_V0.txt

1 directory, 2 files</code></pre>

Yes , we have all the required files.

Now lets verify the configuration files , whether they have all the required information or not :
<pre class="highlight"><code><span class="c"># Display the local configuration of CustomerClone repo</span>
[CustomerClone]$ cat .git/config
Output :
[core]
	repositoryformatversion = 0
	filemode = true
	bare = false
	logallrefupdates = true
[submodule]
	active = .
[remote "origin"]
	url = https://github.com/git-user/Customer.git
	fetch = +refs/heads/*:refs/remotes/origin/*
[branch "master"]
	remote = origin
	merge = refs/heads/master
<span style="color:#c5860b">[submodule "TextFileLogger"]
	url = https://github.com/git-user/TextFileLogger.git</span>

<span class="c"># Display the .gitmodules from CustomerClone repo</span>
[CustomerClone]$ cat .gitmodules
Output :
<span style="color:#c5860b">[submodule "TextFileLogger"]
	path = TextFileLogger
	url = https://github.com/git-user/TextFileLogger.git</span>

<span class="c"># Display .git file from TextFileLogger submodule</span>
[CustomerClone]$ cd TextFileLogger/
[CustomerClone/TextFileLogger]$ cat .git
Output :
<span style="color:#c5860b">gitdir: ../.git/modules/TextFileLogger</span></code></pre>

Yes .git/config and .gitmodules file has information about *TextFileLogger*.
And TextFileLogger/.git file has the gitdir reference to *TextFileLogger* repository meta data.

Lets check the status of *TextFileLogger* submodule : 

<pre class="highlight"><code><span class="c"># Display the status of submodule repo</span>
[CustomerClone/TextFileLogger]$ git status
<span style="color:#ef2929">HEAD detached at cf93a5d</span>
nothing to commit, working tree clean</code></pre>

Also we saw that , *TextFileLogger* repo has the detached head as commit id(SHA1 hash) **cf93a5d** .
Please refer to the [Detached Head in Git](../Art-5/detachedhead.md) article for more information.  

OK.. , Everything is fine now..

So .. in this article we learned about :
* What is a submodule
* How to add a submodule to a repository
* How to clone a repository with submodules

In next article [Git Submodule-II](../Art-4/submodule2.md) we will discuss more on sumbodules.

Happy Learning :)  

[Home](https://debbiswal.github.io/Tech-BITE/) \| [Back](https://debbiswal.github.io/Tech-BITE/#git)  
