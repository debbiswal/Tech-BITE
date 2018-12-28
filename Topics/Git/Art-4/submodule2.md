[Home](https://debbiswal.github.io/Tech-BITE/) \| [Back](https://debbiswal.github.io/Tech-BITE/#git)  

## Git Submodules - II {Draft Version}
In my previous article [Git Submodule-I](../Art-3/submodule.md) , we have discussed the below points :
* How to add a submodule to a repo
* Cloning a repo with its submodules

Today I will discuss more points...

Lets start .. 

### Getting an update from Submodule repo
```bash
# Add a dummy file Logger_V1.txt

[TextFileLogger]$ echo "Logger_V1" >> Logger_V1.txt
[TextFileLogger]$ git add Logger_V1.txt
[TextFileLogger]$ git commit -m "added Logger_V1.txt"
Output :
[master 4027d5c] added Logger_V1.txt
 1 file changed, 1 insertion(+)
 create mode 100644 Logger_V1.txt
 
 # Add a dummy file Logger_V2.txt

[TextFileLogger]$ echo "Logger_V2" >> Logger_V2.txt
[TextFileLogger]$ git add Logger_V2.txt
[TextFileLogger]$ git commit -m "added Logger_V2.txt"
Output :
[master f1118c0] added Logger_V2.txt
 1 file changed, 1 insertion(+)
 create mode 100644 Logger_V2.txt
 
```



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
