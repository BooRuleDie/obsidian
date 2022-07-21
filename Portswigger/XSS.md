# What is XSS 
Cross Site Scripting (aka XSS) is a vulnerability that allows attacker to run his own malicious javascript in target's website. XSS can be used to act like victim and carry out some actions with victim's privileges. Also via XSS we can access victim's sensitive datas.

```html
<h1>test</h1>

<script>
	document.location.href = "https://google.com";
</script>

```

For example code below redirect users to google.com whenever page is visited. Same thing could be achieved via **Stored XSS.**

# PoC of XSS
Most of the time `alert()` function is used to prove whether an application is vulnerable to XSS or not. If browser returns an alert box after you entered the payload, then it means there is XSS. However if browser is chrome, `alert()` may not work, so it's also recommended to use `print()` instead of `alert()`.

If you're testing for XSS in a web application, it'd be best to try your working payload in all browsers.

# Types of XSS
1. **Reflected XSS:** malicious js code comes from HTTP request.
2. **Stored XSS:** malicious js code comes from web app's database.
3. **DOM XSS:** vulnerability exists in client-side code rather than server-side code.

### Reflected XSS
Reflected XSS arises when application returns data that is being passed from HTTP request, within its response. Attacker can inject his own malicious js code inside that data and waits to see if it worked or not.

##### Example of reflected XSS

**normal request**
```html
https://insecure-website.com/status?message=All+is+well. <p>Status: All is well.</p>
```
**XSS payload injected request**
```html
https://insecure-website.com/status?message=<script>/*+Bad+stuff+here...+*/</script> <p>Status: <script>/* Bad stuff here... */</script></p>
```

### Stored XSS
Since Stored XSS payload comes from database, whenever injected page is visited XSS will be triggered without making any further effort. So this is where it's different from reflected XSS. In order to perform reflected XSS, you need to share malicious link with the victim.

Stored XSSs can be found in comment sections, social media posts, chat boxes ...

### DOM XSS

DOM (Document Object Model), is a web API that allows you to read or manipulate HTML, XML documents. It basically converts these document in a tree structure and with the help of a programming language you can manipulate everything in this structure.

![[Pasted image 20220703105602.png]]

Code below uses DOM to manipulate contents within `<p>` tags.
![[Pasted image 20220703110248.png]]

DOM can be used to access, update data that's being passed from frontend. If data is accessed and updated in an unsafe manner and updated data is being shown in frontend after the processes, DOM XSS may arise.
```js
var search = document.getElementById('search').value;
var results = document.getElementById('results');
results.innerHTML = 'You searched for: ' + search;
```

Code above, gets what is entered to search bar and display it on frontend in "You searched for: `input`" format. However attacker can try to inject malicious js into that search bar, if attack works, frontend could be as following:
```html
You searched for: <img src=1 onerror='/* Bad stuff here... */'>
```

# What is XSS capable of ? Impact ?
* Masquerade as victim user.
* Access authentication credentials of victim.
* Carry out actions that victim user is capable of.

Impact depends on the compromised application. If everyone is anonymous user and application doesn't process any sensitive information, then impact of XSS in this application is low. However impact of XSS in  banking, health, government applications, would be critical.

# How to Prevent XSS ?
* **Filter Input**
* **Encode output data** (HTML, URL encodings)
* **Appropriate Response Headers** (Content-Type, X-Content-Type-Options: nosniff) 
* **Content Security Policy** (CSP)

---

#  DOM XSS in `document.write` sink using source `location.search`

[**Write-Up**](https://medium.com/@booruledie/portswigger-labs-dom-xss-1-2d366003eb85)

---
# DOM XSS in `document.write` sink using source `location.search` inside a select element

[**Write-Up**](https://medium.com/@booruledie/portswigger-labs-dom-xss-2-c4a8a619b6dc)

---
# Lab: DOM XSS in `innerHTML` sink using source `location.search`

[**Write-Up**](https://medium.com/@booruledie/portswigger-labs-dom-xss-3-dc31977a837a)

---
# Lab: DOM XSS in jQuery anchor `href` attribute sink using `location.search` source

[**Write-Up**](https://medium.com/@booruledie/portswigger-labs-dom-xss-4-ef013744ba90)

---
# Lab: Stored XSS into anchor `href` attribute with double quotes HTML-encoded

[**Write-Up**](https://medium.com/@booruledie/portswigger-labs-stored-xss-2-6a45cf582953)

---
# Lab: DOM XSS in AngularJS expression with angle brackets and double quotes HTML-encoded

[**Write-Up**](https://medium.com/@booruledie/portswigger-labs-dom-xss-5-50ced5a68c91)

---
# Lab: Reflected DOM XSS
[**Write-Up**](https://medium.com/@booruledie/portswigger-labs-dom-xss-6-f44cb6ecf86d)

---
# Lab: Stored DOM XSS
[**Write-Up**](https://medium.com/@booruledie/portswigger-labs-dom-xss-7-6589fe207f80)

---
# Lab: Exploiting XSS to perform CSRF
[**Write-Up**](https://medium.com/@booruledie/portswigger-labs-csrf-xss-6d59fb849939)

---
# Lab: Reflected XSS into HTML context with most tags and attributes blocked
[**Write-Up**](https://medium.com/@booruledie/portswigger-labs-reflected-xss-4-6f7b6c92c12f)

---
# Lab: Reflected XSS into HTML context with all tags blocked except custom ones
[**Write-Up**](https://medium.com/@booruledie/portswigger-labs-reflected-xss-5-3b25b71aa65a)

---

# Lab: Reflected XSS with some SVG markup allowed
[**Write-Up**](https://medium.com/@booruledie/portswigger-labs-reflected-xss-6-4ec3fc64b287)

---
#  Lab: Reflected XSS in canonical link tag
[**Write-Up**](https://medium.com/@booruledie/portswigger-labs-reflected-xss-7-7ee98d284f6c)

---
# Lab: Reflected XSS into a JavaScript string with single quote and backslash escaped
[**Write-Up**](https://medium.com/@booruledie/1be71da713ae)

---
# Lab: Reflected XSS into a JavaScript string with angle brackets and double quotes HTML-encoded and single quotes escaped
[**Write-Up**](https://medium.com/@booruledie/portswigger-labs-reflected-xss-9-10749c744d6c)

---
# Lab: Stored XSS into `onclick` event with angle brackets and double quotes HTML-encoded and single quotes and backslash escaped
[**Write-Up**]()