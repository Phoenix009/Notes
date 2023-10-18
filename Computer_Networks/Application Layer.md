 
### Network Application Architectures:
From the application developer perspective, ==the network architecture is fixed== and provides a specific set of services to applications.

The ==application architecture is designed by the application developer== and dictates how the application is structured over the various end systems.

Two predominant architectural paradigms:
1. Client Server Architecture
2. Peer-to-Peer Architecture

#### Client-Server Architecture:
There is an always-on host, called the server which services request from many other hosts called clients.

The server has a fixed, well known IP address. Because the server has a fixed address and is always on, a client can always contact the server by sending a packet to the servers IP Address.

A single server is incapable of keeping up with all the requests from clients. For this reason a data centre, housing a large number of hosts, is often used to create a powerful virtual server.

#### P2P Person-To-Person Architecture:
The application exploits direct communication between pairs of intermittently connected hosts, called peers.

Each host acts as both a client and a server simultaneously.

One of the most compelling feature of P2P is their ==self-scalability==.

Peers are intermittently connected and change IP address. leads to ==complex management==.


### Processes Communicating:
A process is a program that is running within an end system. 

We are interested in how processes running on different hosts communicate.

#### Client and Server processes:
The process that initiates the communication is labeled as the client. The process that waits to be contacted to begin the session is the server.

##### socket - Interface between the Process and Computer Network:
A socket is the ==interface between the application layer and the transport layer== within a host. It is also referred to as the Application Programming  Interface (API).

Any message sent from one process to another must go through the underlying network.

##### Addressing Processes:
To identify the receiving process, two pieces of information needs to be specified:

1. address of the host: `IP Address`
2. identifier that specifies the receiving process: `Port#`
Use `cat /etc/services` to print the list of services and their `Port#`
3. `IPAdress:Port#` is reffered to as `Socket ID`


### Transport Services Available to Applications:
Many networks, including the internet provide more than one transport layer protocol. When we develop an application, we must choose one of the available transport layer protocols. 

*How do we make a choice?*
We would study the services provided by the available transport layer protocols.

*What are the services that a transport layer protocol can offer to applications invoking it?*

We can broadly classify the possible services along four dimensions:

#### 1. Reliable Data Transfer / Integrity:

If a protocol can guarantee that the data sent by one end of the application will be delivered correctly and completely to the other end of the application, it is said to provide reliable data transfer.

When a transport layer protocol does not provide reliable data transfer some of the data sent by the sending process may never arrive at the receiving process. This may be acceptable for loss-tolerant applications.

#### 2. Throughput:
In the context of communication, throughput is the rate at which the sending process can deliver bits to the receiving process.

Transport protocols can guarantee available throughout at some specified rate. With such a service, the application could request a guaranteed throughput of $r$ bits/sec

Applications that have throughput requirements are said to be bandwidth-sensitive applications. Many current multimedia applications are bandwidth sensitive.

Elastic/Bandwidth-insensitive applications on the other hand can make use of as much throughput that is available to them. ex: Electronic mail.

#### 3. Timing:

A transport layer protocol can also provide timing guarantees. Such a service would be appealing to interactive real-time applications, all of which require tight timing constraints on data delivery in order to be effective. ex: Interactive online games.

For non-real-time applications, lower delay is always preferable to higher delay, but no tight constraint is place on the end-to-end delays.

#### 4. Security:

A transport protocol can provide an application with one or more security services. 

A transport protocol can encrypt all data transmitted by the sending process, and in the receiving host, the transport layer protocol can decrypt the data before delivering the data to the receiving process.

A transport protocol can also provide other services in addition to confidentiality, including data integrity and end-point authentication.


### Transport Services provided by the Internet:

#### 1. TCP Services:

The TCP service model includes a connection-oriented service and a reliable data transfer service.

##### 1. Connection-Oriented Service: 

TCP has the client and server exchange transport layer control information with each other before the application level messages begin to flow. 

After the handshaking, a TCP connection is said to exist between the sockets of the two processes.

When the application finishes sending messages, it must tear down the connection.

##### 2. Reliable Data Transfer:

The communicating process can rely on TCP to deliver all data without error and in the proper order.

##### 3. Congestion Control:
Throttle sender when network gets overloaded

##### 4. Flow control:
Throttle sender when receiver gets overloaded


#### 2. UDP Services:

UDP is a lightweight transport protocol, providing minimal services. It is connectionless and provides unreliable data transfer service.

UDP provides no guarantee that the message will ever reach the receiving process. Furthermore, messages that do arrive at the receiving process may arrive out of order.

### [[Web and HTTP]]
### [[DNS - Internets Directory Service]]
### [[Email]]
### [[Socket Programming]]
