![[Pasted image 20220725224037.png]]

# Bandit1
There is a file named "-". However we can't just use cat here because it starts taking input when you enter `cat -` . This is command that worked for us:
```bash
grep -R . | grep -v "^\."
```

Or we can use absolute path to take the content of the file:
```bash
cat `pwd`/-
```

# Bandit5
Question tells that the file we're looking for has following features:
* Human Readable
* 1033 bytes in size
* Not executable

So following find command will work for us:
```bash
cat `find . \! -executable -size 1033c`
```

# Bandit6
![[Pasted image 20220726174301.png]]

Command I used:
```bash
find / -user bandit7 -group bandit6 -size 33c 2>/dev/null
```

# Bandit7
![[Pasted image 20220726174453.png]]

```bash
cat data.txt | grep millionth
```

# Bandit8
![[Pasted image 20220726174615.png]]

```bash
cat data.txt | sort | uniq -u
```

`uniq -u` shows lines that occurs only 1 time, however in order to make it work, all lines should be ordered. That's why we use `sort before it.

# Bandit9
![[Pasted image 20220726175414.png]]

```bash
cat data.txt | strings | grep =
```

# Bandit10
![[Pasted image 20220726175522.png]]

```bash
cat data.txt | base64 -d
```

# Bandit11
![[Pasted image 20220726180406.png]]

```bash
cat data.txt | tr 'A-Za-z' 'N-ZA-Mn-za-m'
```

Note that following command would not have worked:
```bash
cat data.txt | tr 'A-Za-z' 'N-Mn-m'
```

Because `tr` thinks that you specified (n,m,N,M) for second set. However it should be letters from N to M and n to m, so you do following:

*  "**A**-**Z**" -> "**N**-ZA-**M**"
* "**a**-**z**" -> "**n**-za-**m**"

# Bandit12
![[Pasted image 20220726180920.png]]

First I converted hexdump into binary with xxd:
```bash
xxd -r data.txt out
```

Then, I've done lots of unpacking with gzip, bzip2 and tar. Note that in order to extract a file, you need to give it correct extension beforehand.

# Bandit13
![[Pasted image 20220726202633.png]]

This is how I got privateKey into my local machine:
```bash
scp -P 2220 bandit13@bandit.labs.overthewire.org:/home/bandit13/sshkey.private .
```

Then, specified private key when authentication:
```bash
ssh bandit14@bandit.labs.overthewire.org -p 2220 -i sshkey.private
```

Don't forget to change permissions of file as 600.

# Bandit14
![[Pasted image 20220726204804.png]]

I send string by using nc:
```bash
echo "4wcYUJFw0k0XLShlDzztnTBHiqxU3b3e" | nc localhost 30000
```

Don't forget that you need to in machine in order to get the password, otherwise it doesn't return you anything.

# Bandit15
![[Pasted image 20220727182209.png]]

This is the command we're going to use:
```bash
openssl s_client –connect localhost:30001
```

# Bandit16
![[Pasted image 20220727183312.png]]

First I found open ports in range of 31k to 32k by using nmap like so:
```bash
nmap -p 31000-32000 -T4 localhost
```

Then I tested all ports with openssl command we've just learned:
```bash
openssl s_client -connect localhost:<port>
```

It gave me RSA private key, and I logged into bandit17 with using that key.
```bash
ssh bandit17@bandit.labs.overthewire.org -p 2220 -i bandit17
```

# Bandit17
![[Pasted image 20220727185745.png]]

`diff` can be used here, it's a tool to compare files line by line.
```bash
diff passwords.old passwords.new
```

# Bandit18
![[Pasted image 20220727190711.png]]

By using -T flag we can make ssh to not execute .bashrc file when you logged in.
```bash
ssh bandit18@bandit.labs.overthewire.org -p 2220 -T
```

# Bandit19
![[Pasted image 20220727191021.png]]

There is a file that has SUID , it runs a command with privileges of bandit20 user.
![[Pasted image 20220727191219.png]]

So if we run following command:
```bash
./bandit20-do cat /etc/bandit_pass/bandit20
```

We can retrieve password of bandit20.

# Bandit20
![[Pasted image 20220727191524.png]]

First, I thought that there was a port that listens for us to send bandit20's password, then I realized that I need to open a listening port with netcat, then forward the password as server:
```bash
nc -lnvp 9999
```

```bash
./suconnect 9999
```

Sends password of bandit20, and the output:
![[Pasted image 20220727192816.png]]

# Bandit21
![[Pasted image 20220727192930.png]]

I saw a cronjob that runs with the privileges of user bandit22, and all it was doing, was catting password of bandit22 into a random file in /tmp. So I catted it out and retrieved password.

# Bandit22
![[Pasted image 20220727193513.png]]

In the /etc/cron.d directory I found a old cronjob that runs as user bandit23.
![[Pasted image 20220727194410.png]]

What it actually does it creating a string with whoami command *(since cronjab run it in past, it'll be bandit23)*, then put the string into md5 function, and taking the hash. This hash is going to be the file where password of bandit23 will be put in /tmp directory.

So following command will just give us the password:
```bash
cat /tmp/`echo "I am user bandit23" | md5sum | cut -d " " -f1`
```

# Bandit23
![[Pasted image 20220727194903.png]]

