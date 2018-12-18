[:house:Home](https://github.com/debbiswal/Articles) | [Back](https://github.com/debbiswal/Articles/blob/master/README.md#git)

## Submodule {DRAFT VERSION}  

What is Submodule 
Explain with diagram  , how two different repo can refer to different version of submodule  
How to add a submodule to existing repo   
  show output of git status , gif diff --cache , cat .gitmodule , cat .git/configure  
How to clone a repo , issues with submodules while cloning , commands to be used , recursive  
Modifying submodule , commiting mainmodule without commiting submodule  
How to derefer a submodule  
How to refere to different version  
How to switch to a different submodule  
How to remove a submodule  


### Disadvantages of Submodule  
* We’ve been talking about how susceptible Git Submodules are towards inconsistencies. That seems to be almost the only problem that threatens Git submodules. Listed here are some of the scenarios this issue occur :
  * When one of the collaborators have made changes to the submodule but doesn’t push the submodule repo, other collaborators will not be in sync.
  * When one of the collaborators have made commit changes to the submodules if others don’t use the ‘git submodule update’ command. The repo will have inconsistent commits.
  * If two different branches are being merged, the submodules remain unchanged and submodule update command must be used without fail.
* It’s almost impossible to track the entire project at a time when using submodules. Changes in the submodules aren’t tracked by the main module and must change the current working directory to look at the changes.  
* It’s hard to use IDEs when using Git Submodules and developers are forced to use the shell to maintain consistency.  



Happy Learning :smiley:  

[:house:Home](https://github.com/debbiswal/Articles) | [Back](https://github.com/debbiswal/Articles/blob/master/README.md#git)
