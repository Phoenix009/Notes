
HTTP is implemented in two programs: a client program and a server program. The client and server program running on two different hosts exchange HTTP messages. HTTP defines the structure of these messages and how the client and server exchanges the messages.

HTTP defines how web clients requests web pages from web servers and how servers transfers the web pages to clients.

==HTTP uses TCP as its underlying transport protocol== and is a stateless protocol. It stores no information about the clients.

#### Non-Persistent and Persistent Connections:

Application developer needs to make an important decision, should each request/response pair be sent over a separate TCP connection (non-persistent connections), or should all of the requests and their corresponding responses be sent over the same TCP connection (persistent connections).


#### HTTP Request:
```
GET /somedir/page.html HTTP/1.1
Host: www.someschool.edu
Connection: close
User-agent: Mozilla/5.0
Accept-language: fr
```

- `Host`: specifies the host on which the object resides
- `Connection`: specifies that it does not want to have a persistent connection
- `User-Agent`: This head er line is useful because the server can actually send different versions of the same object to different types of user agents.
- `Accept-Language`:  is a content-negotiator header and is the user preference to receive a French version of the object.


#### HTTP Response:
```
HTTP/1.1 200 OK
Connection: close
Date: Tue, 09 Aug 2011 15:44:04 GMT
Server: Apache/2.2.3 (CentOS)
Last-Modified: Tue, 09 Aug 2011 15:11:03 GMT
Content-Length: 6821
Content-Type: text/html
(data, data, ...)
```

- *Status Line*: Has three fields, the protocol version, status code and a status message
	HTTP Status Code:
	1. `1XX` - Informational
	2. `2XX` - Success
	3. `3XX` - Redirection
	4. `4XX` - Client Error
	5. `5XX` - Server Error
- *Header Lines*:
	- `Date`: Specifies the time and date when the HTTP response was created and sent by the server
	- `Last-Modified`: indicates the time and date when the object was created or last modified. Is critical for object caching
- *Body*: The actual data being sent


#### Cookies:

A HTTP server is stateless. However, it is often desirable for a website to identify users, either because the server wishes to restrict user access or because it wants to server content as a function of the user identity. For this purpose, HTTP uses cookies.

It has four components:

1. Cookie header line in HTTP Response
2. Cookie header line in HTTP Request
3. Cookie file on users end-system managed by browser
4. Back end database at the website.

Cookie can be used to create a user session layer on top of stateless HTTP. They are controversial because they can also be considered as an invasion of privacy

#### Web Caching:

A Web Cache, also called a proxy server, is a network entity that satisfies HTTP requests on the behalf of an origin server.

Browsers can be configured so that all of the users HTTP requests are first directed to the Web Cache.

Web Caching has seen deployment in the Internet for two reasons.

1. A Web cache can substantially reduce the response time for a client response
2. Web caches can substantially reduce the traffic on an institutions access link to the internet.

Are there any problems when using HTTPs? [https://stackoverflow.com/questions/174348/will-web-browsers-cache-content-over-https](https://stackoverflow.com/questions/174348/will-web-browsers-cache-content-over-https)

#### Conditional Get:

HTTP has a mechanism that allows cache to verify that its objects are up to date. The mechanism is called the Conditional Get.

The conditional GET request has an `If-Modified-Since` header line. Which indicates to the server to only send the object if it was not modified after the specified date

#### Authentication:
