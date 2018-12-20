[Home](https://debbiswal.github.io/Articles/) \| [Back](https://debbiswal.github.io/Articles/#submodule)  

## Submodule {DRAFT VERSION}  

What is a Submodule?  
A submodule is an external git repository , which we refer and use in our repository as a sub-repository.  
In simple terms , git submodules as shared libraries or plugins , which we use in our project , by refering them  to reuse the existing code.  

These sub modules exists as an independent repo within our main(parent) repo.  
As submodules are independent repo on its own ,we also can modify and commit changes to these submodules.  

Lets start with an example :  
Say , I have a repository **Customer** and **Product**.  
Also , I have a repo **TextFileLogger** and **DBLogger**.  

Now , I want to reuse the **TextFileLogger** in my **Customer** and  **Product** repo.  
For this , I can copy the contents of **TextFileLogger** and paste it inside my **Customer** and  **Product** repo.  
But , with this approach I will not be able to track the changes made to **TextFileLogger**.  
Every time , I need to check for updates happning on **TextFileLogger** and copy paste them in my **Customer** and  **Product** repo , which is cumbersome.  

So in summary , the problems with this approach are:  

* The original reference is lost. When we copy and paste code, there’s no reference back to the original spot where the code was found, and it’s easily forgotten about.  

* Updates aren’t easily integrated. When changes are made to the original code we copied, it becomes very hard to track what’s changed so we can apply those changes back to our cut and pasted code. Some third party libraries can have thousands of lines of code, spread across hundreds of files, and it’s impossible to keep things synchronized manually.  

* Version information isn’t maintained. Proper software development practices call for versioning releases of our code. We will find this consistent in third party libraries we use in our projects. When we copy and paste code, there’s no easy way to know we are using version 1.0.0 of library **TextFileLogger**  and how will we remember to update our code when version 1.0.1 is released?

The simplest solution , is to refer **TextFileLogger** repo from our repo. And pull/fetch the **TextFileLogger** repo when-ever required.

Now , say there are some changes happend to **TextFileLogger** :  
* change-1 : a new file LOGGING_V1.txt is added with commit id - COMMIT-ID-01
* change-2 : a new file LOGGING_V2.txt is added with commit id - COMMIT-ID-02

We will upgrade the **TextFileLogger** in **Product** repo , to COMMIT-ID-02.  
But **Customer** repo will only be able to use the COMMIT-ID-01 , due to some backward compatibility of other modules inside it.  

This kind of facility makes use of submodules simpler , as we can refer to different commit state in our different projects/repositories.  

![repo](images/img1.png)  

Create TextFileLogger,DBLogger repo with dummy content


Explain with diagram  , how two different repo can refer to different version of submodule  
How to add a submodule to existing repo   
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

[Home](https://debbiswal.github.io/Articles/) \| [Back](https://debbiswal.github.io/Articles/#submodule)  
