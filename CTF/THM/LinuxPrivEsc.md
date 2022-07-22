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

