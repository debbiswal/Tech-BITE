[Home](https://debbiswal.github.io/Tech-BITE/) \| [Back](https://debbiswal.github.io/Tech-BITE/#git)  

## Git Submodules - II {Draft Version}
In my previous article [Git Submodule-I](../Art-3/submodule.md) , we have discussed the below points :
* How to add a submodule to a repo
* Cloning a repo with its submodules

Today I will discuss more points...

Lets start .. 

### Getting an update from Submodule repo

Lets first check the *TextFileLogger* repo and see to which commit SHA1 , the head points 
```bash
# Get into the TextFileLogger repo
$ cd TextFileLogger

# Print the log of repo
[TextFileLogger]$ git log --oneline
cf93a5d (HEAD -> master, origin/master, origin/HEAD) Create Logger_V0.txt
```
We can see that , the HEAD is as master . And the commit SHA1 is **cf93a5d**

Lets add few files to *TextFileLogger* repo :
```bash
# Add a dummy file Logger_V1.txt
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
[TextFileLogger]$ git push
```

Now lets see the log :
```bash
[TextFileLogger]$ git log --oneline
7125a58 (HEAD -> master, origin/master, origin/HEAD) added Logger_V2.txt
8c29b0f added Logger_V1.txt
cf93a5d Create Logger_V0.txt
```

Suppose we now want to get these two commits inside our *TextFileLogger* submodule in *Customer* repo.  
To achieve this, we need to update its local repo, starting by moving into its working directory so it becomes our active repo.
```bash
# come out of TextFileLogger repo
[TextFileLogger]$ cd ..

# get into Customer repo
$ cd Customer

# get into TextFileLogger submodule folder
[Customer]$ cd TextFileLogger
[Customer/TextFileLogger]$

# print the log to get the status of HEAD and commit SHA1
[Customer/TextFileLogger]$ git log --oneline
cf93a5d (HEAD -> master, origin/master, origin/HEAD) Create Logger_V0.txt
```

Lets try to update the submodule with remote updates :
```bash
[Customer/TextFileLogger]$ git pull
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
 create mode 100644 Logger_V2.txt
```
But there is a problem with *git pull* command . We got all the updates.
What if we want to get a specific commit .. say the commit for **Logger_V1.txt** file .
Then we have to checkout to a specific commit SHA1.

But first we have to revert back the pull chnages :
```bash
# Check the HEAD position
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
cf93a5d (HEAD -> master) Create Logger_V0.txt
```
OK.. all set now.
Lets update the TextFileLogger submodule to a specific commit
<pre class="highlight"><code>[Customer/TextFileLogger]$ git fetch

<span class="c"># get the SHA1 details from origin</span>
[Customer/TextFileLogger]$ git log --oneline origin/master
7125a58 (origin/master, origin/HEAD) added Logger_V2.txt
8c29b0f added Logger_V1.txt
cf93a5d (HEAD -> master) Create Logger_V0.txt

<span class="c"># checkout to SHA1 8c29b0f , to get Logger_V1.txt commit</span>
[Customer/TextFileLogger]$ git checkout -q 8c29b0f

<span class="c"># check the status</span> 
[Customer/TextFileLogger]$ git status
<b>HEAD detached at 8c29b0f</b>
nothing to commit, working tree clean

<span class="c"># check the log</span>
[Customer/TextFileLogger]$ git log --oneline
8c29b0f (HEAD) added Logger_V1.txt
cf93a5d (master) Create Logger_V0.txt</code></pre>

Our submodule is updated with selected commit. So now we can see the change in parent repo(Customer)
<pre class="highlight"><code> [Customer/TextFileLogger]$ cd ..
[Customer]$ ls
Customer_V0.txt  TextFileLogger
[Customer]$ git status
On branch master
Your branch is up to date with 'origin/master'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   TextFileLogger <b>(new commits)</b>

Submodules changed but not updated:

* TextFileLogger cf93a5d...8c29b0f (1):
  <b>  added Logger_V1.txt</b>

no changes added to commit (use "git add" and/or "git commit -a")</code></pre>

We can see that , new commits has been made. It means our reference submodule has been changed , or we made some changes to local submodule code.

The display of change details for submoule   is enabled by our *status.submoduleSummary = true* setting  , which we did earlier. 
It explicitly states the introduced commits (as they use a right-pointing angle bracket >) since our last parent repo (*Customer*) commit that had touched the submodule.


Pending .... =>
Adding submodule from a specific branch or commit
show output of git status , gif diff --cache , cat .gitmodule , cat .git/configure  
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
