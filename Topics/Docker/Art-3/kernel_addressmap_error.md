[:house:Home](https://github.com/debbiswal/Articles) | [Back](https://github.com/debbiswal/Articles/blob/master/README.md#docker)

# Kernel address map issue in Docker container

Below is the error , which you may get while working with docker container.

**Error :**
```shell
WARNING: Kernel address maps (/proc/{kallsyms,modules}) are restricted,
check /proc/sys/kernel/kptr_restrict.

Samples in kernel functions may not be resolved if a suitable vmlinux
file is not found in the buildid cache or in the vmlinux path.

Samples in kernel modules won't be resolved at all.

If some relocation was applied (e.g. kexec) symbols may be misresolved
even with a suitable vmlinux or kallsyms file.

Couldn't record kernel reference relocation symbol
Symbol resolution may be skewed if relocation was used (e.g. kexec).
Check /proc/kallsyms permission or run as root.
```  

**Solution :**
```shell
cat /proc/sys/kernel/kptr_restrict : the output should be 1 or 2.
```  

We have to make it 0.

Try :  
```shell
sudo sh –c ‘echo 0 > /proc/sys/kernel/kptr_restrict’
```  


If this is giving ‘Read only’ permission issue, then

Start the container with ‘--privileged’ argument . like : 
```shell
docker run –privileged ….
```  

Then use sysctl command to change the value of kptr_restrict
```shell
sudo sysctl –w kernel.kptr_restrict=0
```  

It will resolve the issue.  

Below is the excerpt from kernel documentation regarding kptr_restrict :  
```shell
kptr_restrict:

This toggle indicates whether restrictions are placed on exposing kernel addresses via /proc and other interfaces.

When kptr_restrict is set to 0 (the default) the address is hashed before printing. (This is the equivalent to %p.)

When kptr_restrict is set to (1), kernel pointers printed using the %pK format specifier will be replaced with 0's unless the user has CAP_SYSLOG
and effective user and group ids are equal to the real ids. This is because %pK checks are done at read() time rather than open() time, so
if permissions are elevated between the open() and the read() (e.g via a setuid binary) then %pK will not leak kernel pointers to unprivileged
users. Note, this is a temporary solution only. The correct long-term solution is to do the permission checks at open() time. Consider removing
world read permissions from files that use %pK, and using dmesg_restrict to protect against uses of %pK in dmesg(8) if leaking kernel pointer
values to unprivileged users is a concern.

When kptr_restrict is set to (2), kernel pointers printed using %pK will be replaced with 0's regardless of privileges.
```  

Happy Learning :smiley:  

[:house:Home](https://github.com/debbiswal/Articles) | [Back](https://github.com/debbiswal/Articles/blob/master/README.md#docker)
