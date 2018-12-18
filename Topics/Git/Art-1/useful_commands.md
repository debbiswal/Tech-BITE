[:house:Home](https://github.com/debbiswal/Articles) | [Back](https://github.com/debbiswal/Articles/blob/master/README.md#git)

# Useful Commands

**How to delete git reference from a folder and sub folder ?**  
```bash
find . -name ".git" -type f -delete  
find . -name ".gitattributes" -type f -delete  
find . -name ".gitignore" -type f -delete  
```

**Configuration**    
```git
# show current values for all global configuration parameters 
git config --list --global

# let git automatically correct typos such as "comit" and "pussh." 
git config --global help.autocorrect 1

# set a username globally 
git config --global user.name "username"`

# set an email address globally
git config --global user.email "email@provider.com"

# always --prune for git fetch and git pull
git config --global fetch.prune true

# remove the previously set username globally
git config --global --unset user.name

# color the git console
git config color.ui true

# set the tool used by git for diffing globally
git config --global diff.tool mytool

# set the tool used by git for merging globally
git config --global merge.tool mytool

# remove the previously set configuration value globally
git config --global --unset myparameter

# allows populating the working directory sparsely, that is, cloning only certain directories from a repository
git config core.sparseCheckout true

# instruct git to retrieve only some directory in addition to those listed in `.git/info/sparse-checkout
echo "some/directory/inside/the/repository" >> .git/info/sparse-checkout

# define which whitespace problems git should recognize(any whitespace at the end of a line, mixed spaces or tabs)
git config --global core.whitespace trailing-space,space-before-tab

# tells Git to detect renames. If set to any boolean value, it will enable basic rename detection. If set to "copies" or "copy", it will detect copies, as well.
git config --global diff.renames copies

# if set, git diff uses a prefix pair that is different from the standard "a/" and "0001/" depending on what is being compared.
git config --global diff.mnemonicprefix true

# always show a diffstat at the end of a merge
git config --global merge.stat true

# no CRLF to LF output conversion will be performed
git config --global core.autocrlf input

# whenever pushing, also push local tags
git config --global push.followTags true

# show also individual files in untracked directories in status queries
git config --global status.showUntrackedFiles all

# always decorate the output of git log
git config --global log.decorate full

# the git stash show command without an option will show the stash in patch form
git config --global stash.showPatch true

# always set the upstream branch of the current branch as the branch to be pushed to when no refspec is given
git config --global push.default tracking
```



[:house:Home](https://github.com/debbiswal/Articles) | [Back](https://github.com/debbiswal/Articles/blob/master/README.md#git)
