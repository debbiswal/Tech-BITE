[Home](https://debbiswal.github.io/Tech-BITE/) \| [Back](https://debbiswal.github.io/Tech-BITE/#git)  

## Git Submodules - II
In my previous article [Git Submodule-I](../Art-3/submodule.md) , we have discussed the below points :
* What is a submodule
* How to add a submodule to a repo
* Cloning a repo with its submodules

Lets discuss below points in this article :
* Getting an update from Submodule repo
* Whats the difference with *diff*
* Pulling a submodule-using repo

### Getting an update from Submodule repo

Lets first check the *TextFileLogger* repo and see to which commit SHA1 , the head points 
<pre class="highlight"><code># Get into the TextFileLogger repo
$ cd TextFileLogger

# Print the log of repo
[TextFileLogger]$ git log --oneline
<span style="color:#a58702">cf93a5d</span> (<span style="color:#ef2929">HEAD -> master, origin/master, origin/HEAD</span>) Create Logger_V0.txt</code></pre>

We can see that , the HEAD is as master . And the commit SHA1 is **cf93a5d**

Lets add few files to *TextFileLogger* repo :
<pre class="highlight"><code># Add a dummy file Logger_V1.txt
[TextFileLogger]$ echo "Logger_V1" >> Logger_V1.txt
[TextFileLogger]$ git add Logger_V1.txt
[TextFileLogger]$ git commit -m "added Logger_V1.txt"
Output :
[master 8c29b0f] added Logger_V1.txt
 1 file changed, 1 insertion(+)
 create mode 100644 Logger_V1.txt
 
# Add a dummy file Logger_V2.txt
[TextFileLogger]$ echo "Logger_V2" >> Logger_V2.txt
[TextFileLogger]$ git add Logger_V2.txt
[TextFileLogger]$ git commit -m "added Logger_V2.txt"
Output :
[master 7125a58] added Logger_V2.txt
 1 file changed, 1 insertion(+)
 create mode 100644 Logger_V2.txt

# Push to master branch
[TextFileLogger]$ git push</code></pre>

Now lets see the log :
<pre class="highlight"><code>[TextFileLogger]$ git log --oneline
<span style="color:#a58702">7125a58</span> (<span style="color:#ef2929">HEAD -> master, origin/master, origin/HEAD</span>) added Logger_V2.txt
<span style="color:#a58702">8c29b0f</span> added Logger_V1.txt
<span style="color:#a58702">cf93a5d</span> Create Logger_V0.txt</code></pre>

Suppose we now want to get these two commits inside our *TextFileLogger* submodule in *Customer* repo.  
To achieve this, we need to update its local repo, starting by moving into its working directory so it becomes our active repo.
<pre class="highlight"><code># come out of TextFileLogger repo
[TextFileLogger]$ cd ..

# get into Customer repo
$ cd Customer

# get into TextFileLogger submodule folder
[Customer]$ cd TextFileLogger
[Customer/TextFileLogger]$

# print the log to get the status of HEAD and commit SHA1
[Customer/TextFileLogger]$ git log --oneline
cf93a5d (HEAD -> master, origin/master, origin/HEAD) Create Logger_V0.txt</code></pre>

Lets try to update the submodule with remote updates :
<pre class="highlight"><code>[Customer/TextFileLogger]$ git pull
remote: Enumerating objects: 7, done.
remote: Counting objects: 100% (7/7), done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 6 (delta 1), reused 6 (delta 1), pack-reused 0
Unpacking objects: 100% (6/6), done.
From https://github.com/debbiswal/TextFileLogger
   cf93a5d..7125a58  master     -> origin/master
Updating cf93a5d..7125a58
Fast-forward
 Logger_V1.txt | 1 +
 Logger_V2.txt | 1 +
 2 files changed, 2 insertions(+)
 create mode 100644 Logger_V1.txt
 create mode 100644 Logger_V2.txt</code></pre>
 
But there is a problem with *git pull* command . We got all the updates.
What if we want to get a specific commit .. say the commit for **Logger_V1.txt** file .
Then we have to checkout to a specific commit SHA1.

But first we have to revert back the pull chnages :
<pre class="highlight"><code># Check the HEAD position
[Customer/TextFileLogger]$ git log --oneline
7125a58 (HEAD -> master, origin/master, origin/HEAD) added Logger_V2.txt
8c29b0f added Logger_V1.txt
cf93a5d Create Logger_V0.txt

# Revert the pull changes
[Customer/TextFileLogger]$ git reset --hard HEAD~1
HEAD is now at 8c29b0f added Logger_V1.txt
[Customer/TextFileLogger]$ git reset --hard HEAD~1
HEAD is now at cf93a5d Create Logger_V0.txt

# verify the HEAD position
[Customer/TextFileLogger]$ git log --oneline
<span style="color:#a58702">cf93a5d</span> (<span style="color:#ef2929">HEAD -> master</span>) Create Logger_V0.txt</code></pre>

OK.. all set now.
Lets update the TextFileLogger submodule to a specific commit
<pre class="highlight"><code>[Customer/TextFileLogger]$ git fetch

<span class="c"># get the SHA1 details from origin</span>
[Customer/TextFileLogger]$ git log --oneline origin/master
<span style="color:#a58702">7125a58</span> (<span style="color:#ef2929">origin/master, origin/HEAD</span>) added Logger_V2.txt
<span style="color:#a58702">8c29b0f</span> added Logger_V1.txt
<span style="color:#a58702">cf93a5d</span> (<span style="color:#55a705">HEAD -> master</span>) Create Logger_V0.txt

<span class="c"># checkout to SHA1 8c29b0f , to get Logger_V1.txt commit</span>
[Customer/TextFileLogger]$ git checkout -q 8c29b0f

<span class="c"># check the status</span> 
[Customer/TextFileLogger]$ git status
<b>HEAD detached at 8c29b0f</b>
nothing to commit, working tree clean

<span class="c"># check the log</span>
[Customer/TextFileLogger]$ git log --oneline
<span style="color:#a58702">8c29b0f</span> (<span style="color:#04aeae">HEAD</span>) added Logger_V1.txt
<span style="color:#a58702">cf93a5d</span> (<span style="color:#55a705">master</span>) Create Logger_V0.txt</code></pre>

Our submodule is updated with selected commit. So now we can see the change in parent repo(Customer)
<pre class="highlight"><code>
[Customer/TextFileLogger]$ cd ..
[Customer]$ ls
Customer_V0.txt  TextFileLogger
[Customer]$ git status
On branch master
Your branch is up to date with 'origin/master'.

Changes not staged for commit:
(use "git add file..." to update what will be committed)
(use "git checkout -- file..." to discard changes in working directory)

<span style="color:red">modified:   TextFileLogger</span> <b>(new commits)</b>
Submodules changed but not updated:

* TextFileLogger cf93a5d...8c29b0f (1):
  <b> > added Logger_V1.txt</b>

no changes added to commit (use "git add" and/or "git commit -a")</code></pre>
We can see that , new commits has been made. It means our reference submodule has been changed , or we made some changes to local submodule code.

The display of change details for submoule   is enabled by our *status.submoduleSummary = true* setting  , which we did earlier.  

It explicitly states the introduced commits (as they use a right-pointing angle bracket >) since our last parent repo (*Customer*) commit that had touched the submodule.

### Whats the difference with *diff*
Till yet we saw how to figureout the changes made by using the *git status* command.
But if we will use *git diff* then what will heppen :

<pre class="highlight"><code>[Customer]$ git diff
<b>diff --git a/TextFileLogger b/TextFileLogger
index cf93a5d..8c29b0f 160000
--- a/TextFileLogger
 +++ b/TextFileLogger</b>
<span style="color:magenta">@@ -1 +1 @@</span>
<span style="color:red">-Subproject commit cf93a5d641a1af6c558762935e8d544c90308e0e</span>
<span style="color:green">+Subproject commit 8c29b0f69bc187441b1fba9627e0060243ba4846</span></code></pre>

But from the above result , we did not understood , what changes has been done to *TextFileLogger* submodule.

So , lets try *submodule=log* argument :
<pre class="highlight"><code>[Customer]$ git diff --submodule=log
Submodule TextFileLogger cf93a5d..8c29b0f:
  <span style="color:green">> added Logger_V1.txt</span></code></pre>
hmmm ... we got more information .

There are no other local changes right now besides the submodule’s referenced commit . 
You can notice that , these changes match almost exactly the lower part of our enhanced git status output.

But it would be better if we can make the *submodule=log* argument global in some configuration.
So that we dont have to use everytime for submodule
Yes we can :
<pre class="highlight"><code> # set the global configuration for submodule log
[Customer]$ <b>git config --global diff.submodule log</b>

# get the difference
[Customer]$ git diff
Submodule TextFileLogger cf93a5d..8c29b0f:
 <span style="color:green">> added Logger_V1.txt</span></code></pre>

Now we can push these changes in main repo
<pre class="highlight"><code>[Customer]$ git commit -am "Setting submodule on Logger_V1 (8c29b0f)"
[Customer]$ git push </code></pre>

### Pulling a submodule-using repo
Do you remember the *CustomerClone* repo , which we cloned from *Customer* repo?
As we have modified the *Customer* repo in previous section , lets take the latest of that :
<pre class="highlight"><code># come out of Customer repo folder
[Customer]$ cd ..
$ cd CustomerClone
# go git pull , to get the latest changes
[CustomerClone]$ git pull
remote: Enumerating objects: 3, done.
remote: Counting objects: 100% (3/3), done.
remote: Compressing objects: 100% (1/1), done.
remote: Total 2 (delta 1), reused 2 (delta 1), pack-reused 0
Unpacking objects: 100% (2/2), done.
From https://github.com/debbiswal/Customer
   e6a4e13..e9049f0  master     -> origin/master
<span style="color:magenta">Fetching submodule TextFileLogger</span>
From https://github.com/debbiswal/TextFileLogger
   cf93a5d..7125a58  master     -> origin/master
Updating e6a4e13..e9049f0
Fast-forward
 TextFileLogger | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)</code></pre>
 
We can see that second half of this display: it’s about the submodule, starting with “Fetching submodule…”.

This behavior became the default with Git 1.7.5, with the configuration setting *fetch.recurseSubmodules* now defaulting to on-demand: if a parent repo gets updates to referenced submodule commits, these submodules get fetched automatically.

Now lets check the status of repo :
<pre class="highlight"><code>[CustomerClone]$ git status
On branch master
Your branch is up to date with 'origin/master'.

Changes not staged for commit:
(use "git add file..." to update what will be committed)
(use "git checkout -- file..." to discard changes in working directory)
<span style="color:red">modified:   TextFileLogger</span> (new commits)

Submodules changed but not updated:

* TextFileLogger 8c29b0f...cf93a5d (1):
  <span style="color:red">< added Logger_V1.txt</span></code></pre>

Did you notice how the angle brackets point left (<)? 
Git found that the current working directory does not have this commit .

This is a problem : if we don’t explicitly update the submodule’s working directory, our next parent repo commit will regress the submodule.

Lets verify again by listing the current working directory :
<pre class="highlight"><code>[CustomerClone]$ tree
.
├── Customer_V0.txt
└── TextFileLogger
    └── Logger_V0.txt

1 directory, 2 files</code></pre>

Yes... we did not get the update for submodule. Else we could have **Logger_V1.txt** file in out *TextFileLogger* folder.

> So , Git auto-fetches, but does not auto-update.

Our local cache is up-to-date with the submodule’s remote, but the submodule’s working directory stuck to its old contents.

Then we need to update our submodule :
<pre class="highlight"><code># update the submodule
[CustomerClone]$ git submodule update --init --recursive
Submodule path 'TextFileLogger': checked out '8c29b0f69bc187441b1fba9627e0060243ba4846'

# lets check the folder contenets
[CustomerClone]$ tree
.
├── Customer_V0.txt
└── TextFileLogger
    ├── Logger_V0.txt
    └── Logger_V1.txt</code></pre>
YES.. its updated. We have all the required files.

So .. in this article we learned about :
* Getting an update from Submodule repo
* Whats the difference with *diff*
* Pulling a submodule-using repo

In next article [Git Submodule-III](../Art-6/submodule3.md) we will discuss more on sumbodules.

Happy Learning :)  

[Home](https://debbiswal.github.io/Tech-BITE/) \| [Back](https://debbiswal.github.io/Tech-BITE/#git)  
