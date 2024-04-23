# SOP - Same Origin Policy
consider the tuple (protocol, hostname, port#) for implementing constraints.

In the earliest days of the Web there were no security considerations for JS. Any document could arbitrarily frame others and gain read/write access.

Without any separation, the attacker could use the handle of the frame in JS to read the entire body of the cross origin resource.

To stop such attacks, all browsers implement the webs most basic security mechanism: The SOP - Same Origin Policy. This policy is used for multiple purposes namely to stop access to cross origin frames, disallow modification of cookies, disallow read access to HTTP responses to other origins when the requests are made through JS.

# XSS - Cross Site Scripting
The SOP protects documents from access by others if the origins do not match. Hence for an attacker the only way to gain access to data from other origins is to get their code running in the target Web site.

This can occur when an application insecurely processes some user provided input eg by echoing user provided string. The attack that abuses this to inject malicious code into another origins code is called Cross Site Scripting.

The effect of such an injection is that now the ==attackers code runs in the origin of a vulnerable application in the browser of their targeted victim.== This can be used to conduct different types of attacks. ex: Steal cookies for session hijacking.

## Server Side XSS:
### Reflected Server Side XSS:
Reflected Server Side XSS vulnerabilities occur when attacker controlled input is reflected into the HTML sent to the client.

#### Attack Steps:
- Once the attacker discovers the reflection of the injected code they now customise the payload in such a way that it has meaning ex: to modify the page to phish a user.
- Once the payload is built, the attacker needs to lure the victim to the site. This can be done by using a link shortening service to hide the suspicious nature of the URL.
- Once the user clicks on the url the browser makes the authenticated request to the server, finally the payload is reflected to the victim where it executes.

### Persistent Server Side XSS:
- Persistent Server Side XSS flaws occur when user provided input is persistently stored within a Web Application and later on retrieved from the data storage.

### Preventing Server Side XSS Vulnerabilities:
There are typically two ways in which XSS can be prevented:
#### Input Sanitisation:
- We refer to input sanitisation as measure that try to remove any potentially harmful strings when data is being sent to the server.
- Usually input sanitising can be applied in two ways:
	- Allow listing: good for security
	- block listing: Meh!
- Input sanitisation generally suffers from an important drawback. At the time of checking the input, it is unclear in what context the data will be used lated. Unless the usage of data is absolutely clear from the moment data enters the application, input sanitisation is prone to bypasses.

#### Output Encoding:
- Rather than attempting to asses the usage of data when it enters the application, output encoding is meant to encode user provided data when it is used. Hence depending on the context of where the data is used the correct encoding function can be used.
- Modern frameworks are pre-equipped with context aware encoding functions.


## Client Side XSS:
These are flaws caused by the insecure usage of attacker controllable data in JS code. Client side XSS allows for an interesting case, as attacker can readily analyse the source code, since everything is delivered to the client for execution.

In essence, an XSS bug is a data flow from attacker controllable sources to dangerous sinks. In the context of client side XSS, there are a number of sinks that have different required payloads.

In general there are three classes of sinks:
1. HTML based sinks: `document.write`, `innerHTML`
2. JS based sinks: `eval`, `setTimeout`, `setInterval`
3. URL based sinks: 

### Reflected Client Side XSS:
The attacker can analyse the source code to find invocations of dangerous functions (sinks) with attacker controlled input (source). XSS can be considered a data flow from attacker controlled sources to dangerous sinks. 

### Persistent Client Side XSS:



# CSP - Content Security Policy
- XSS boils down to execution of attacker created script in vulnerable web site
- CSP is a mechanism that allows developers to specify allowed resources (scripts images and others) through means of the CSP header.

## CSP for Script Content Control:
- For mitigation of XSS, CSPs most important directive is called `script-src`, which controls from where scripts can be loaded.
- By default, if a `script-src` directive is present in a CSP, inline scripts and the usage of eval are forbidden.

|  |  |
| ---- | ---- |
| none | scripts must not be included from anywhere |
| self | scripts can be included from the pages own origin |
| unsafe-eval | re enables eval and its derivatives |
| unsafe-inline | re enables the usage of inline scripts and event handlers. |

# CSRF:
By default, A browser attaches all matching cookies to requests towards any server, irrespective of whether it has the same origin as the document that caused the browser to make the
request or not. 

This behavior can be abused by an attacker to force their victim’s browser into making a state-changing request. We refer to this attack as CSRF - Cross Site Request Forgery.


## Defending against CSRF:
The attack relies on a document that is able to force the browser to make a cross origin request with cookies attached.

That implies that in case of an attack, there is a request that originated from outside the applications origin boundary.

### Origin Checking:
There exists a privacy friendly header called `Origin`. this header is sent by all modern browsers in any cross origin POST request.

hence a website that assumes to only have modern clients can safely rely on the `Origin` header to ensure a request was not caused by another origin.

The solution of relying on the `Origin` header is only secure if we assume browsers fully support this header. If a browser does not support the header, it would naturally not send it along in a cross-origin request. In particular, if the header is missing for a POST request, this implies that the referring page must be same-origin. However, the server would assume that the lack of `Origin` header implies a same-origin request and would possibly let the attack occur.

### CSRF Tokens / Nonces:
The idea of this is straightforward: inside the form, there is an additional, hidden form field with a random token. This token is bound to the users session and stored on the server. when the user submits the form, their browser sends the token alongside the remaining form data. 


# SQL Injection:
SQL separates code and data from each other. In real world applications, this separation is less obvious and often queries are built through the concatenation of developer provided code with user controlled data. 

When such data is not filtered or sanitised it can be chosen such that the database considers parts of the data as code. We refer to this as an SQL injection.

## Blind SQL Injection:
Thus far, our threat model has been that the output of a query can be learned by the attacker. However, this may not necessarily be the case for all SQL injection. 

Even in cases where there is no direct output to the attacker, the attacker can still learn information through so-called blind SQL injections.

## Preventing SQL Injection:
SQL injection is another class of vulnerability caused through the improper separation between code and data. The optimal solution to this problem therefore is to clearly communicate to the database which part of the query is data and which part is code. This is implemented through so-called ==prepared statements==.

