It has three major components:
1. User Agent
	Allows users to read, reply, forward and compose messages.

2. Mail Servers
	Form the core of email infrastructure

3. SMTP - Simple Message Transfer Protocol
	Is the principal application-layer protocol for email. Uses reliable data transfer service of TCP. Uses a client-server architecture. Each mail server behaves as both a client and a server.


### SMTP - Simple Message Transfer Protocol:
SMTP transfers messages from senders mail servers to the recipients mail server. 

We will take a closer look at how SMTP transfers a message from a sending mail server to a receiving mail server.

1. the client creates a ==TCP connection on port 25== at the server
2. the server and client perform application-layer handshaking
	- SMTP client indicates the email address of the sender and recipient
3. client sends the message 
	- SMTP relies on TCP to reliably send the message without errors
4. instructs TCP to close the connection


The following messages are exchanged between the client and the server.
```
C: HELO <hostname>  
S: 250 Hello <hostname>, pleased to meet you  

// For each message
C: MAIL FROM: <senders_address>  
S: 250 <senders_address> ... Sender ok  
C: RCPT TO: <recipient_address>  
S: 250 <recipient_address> ... Recipient ok  
C: DATA  
S: 354 Enter mail, end with “.” on a line by itself 
C: <message>  
C: <message>  
C: .  
S: 250 Message accepted for delivery
// endfor

C: QUIT
```

As part of the dialogue, the client issues five commands:
HELO, MAIL FROM, RCPT TO, DATA and QUIT.

==SMTP uses persistent connections==: If the sending mail server has several messages to sent to the same receiving mail server, it can send all of the messages over the same TCP connection.


### Mail Access Protocols:
SMTP is used to transfer mails from the senders mail server to recipients mail server; SMTP is also used to transfer mail from the senders user agent to the senders mail server.

SMTP cannot be used by the recipients user agent obtain messages because it is a pull operation. A ==mail access protocol== is used to transfer mail from the recipients mail server to recipients user agent.

#### POP3:
The protocol is simple and thus its functionality is rather limited.

POP3 begins when the user agent opens a TCP connection to the mail server on ==port 110==. POP3 progresses through three phases: authorisation, transaction and update.

During the authorisation phase: the user agent sends a username and a password to authenticate the user. 

During the transaction phase: the user agent retrieves messages and can also mark messages for deletion. 

The update phase starts after the user agents issues the quit command. During this phase all the messages marked for deletion are deleted.

During a POP3 session the server maintains some state information: primarily, it keeps track of which user messages are to be deleted.

#### IMAP:
IMAP has more features than POP3. Associates every message with a folder. Recipient can then move the message into a new user-created folder, read the message, delete it and so on.

Important feature of IMAP is that is has commands to allow user agent to obtain components of messages, eg: header. 
