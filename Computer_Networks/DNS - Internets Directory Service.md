**RFC 1034, 1035: can be referred to get deep understanding**

One identifier for a host is its hostname such as www.google.com. However, hostnames provide little, if any information about the location within the Internet of the host.

For these reasons hosts are identified by an IP Address

We need a ==directory service that translates hostnames to IP addresses==. This is the main task of the domain name system DNS. 


The DNS is
1. a ==distributed database== implemented in a hierarchy of DNS servers.
2. an ==application layer protocol== that allows hosts to query the distributed database.

DNS provides few other important services in addition to translating host names to IP Addresses:

1. Host Aliasing: 
A host with a complicated hostname can have one or more alias names. In this case the original hostname is called the canonical hostname. 

DNS can be invoked by an application to obtain the canonical hostname for a supplied alias hostname as well as the IP Address of the host.

2. Load Distribution: 
DNS is also used to perform load distribution among replicated servers. For a replicated server, a set of IP addresses are associated with one canonical hostname.

When client makes a DNS query for a name, DNS responds by giving the entire set of the IP Addresses but rotates the entries.

Client makes request to the first IP Address in the set and thus, DNS rotation distributes the traffic among the replicated servers.


### Distributed Hierarchical Database:

#### Name Servers:
DNS uses a large number of servers organised in a hierarchical fashion and distributed around the world.

To a first approximation, there are three classes of DNS servers.

##### 1. root DNS server:
Authoritative for root zone (`.`). Contains name server mappings for all top-level domains.

*Bootstrapping problem*: How does a resolver find the root servers?
The resolver software is shipped with a *roots hints* file which helps the resolver to find the root domain servers.

##### 2. top-level domain (TLD) DNS server:
These servers are responsible for top-level domains (just below the root level) such as `com`, `org`, etc.

##### 3. Authoritative DNS server:
Every organisation with publicly accessible hosts on the internet must provide publicly accessible DNS records that map the name of those hosts to IP Addresses.

An organisation may choose to implement its own authoritative DNS server to hold these records; alternatively, the organisation can pay to have these records in an authoritative DNS server of some other service provider.


There is another important type of DNS server call the local DNS server. When a host makes a DNS query, the query is sent to the local DNS server, which acts as a proxy, forwarding the query into the DNS server hierarchy.


#### Resolvers:
Issues DNS queries to a name server to resolve a name to an IP Address.

##### Stub Resolver:
"Dumb" resolver software running on the ==local machine==. Sends DNS queries to configured resolver (`/etc/resolv.conf`).

##### Forwarding Resolver:
Acts as a ==proxy==. Receives queries which it forwards to a recursive resolver.

##### Recursive Resolver:
Does the actual heavy lifting. 
Receives queries from the forwarding/stub resolver, recursively queries the servers in DNS hierarchy and returns the resolved answer to the querying resolver.

#### Resolution:

##### Iterative: "I don't know, but ask here"
Contacted server replies with name of server to contact.

##### Recursive: "I don't know, but I'll ask"
Puts burden of name resolution on contacted name server.

Mix of iterative and recursive queries is used to resolve a given hostname.



#### DNS Caching:
DNS extensively exploits DNS caching to improve the delay performance and to reduce the number of DNS messages.

In a query chain, when a DNS server receives a DNS reply, it can cache the mapping in its local memory.

Because hosts and mappings between hostname and IP address are not permanent, DNS server discards cache after a period of time.

Local DNS can cache the IP address thereby allowing local DNS server to bypass hierarchy.



#### DNS Records and Messages:
DNS distributed database stores resource records RRs including RRs that provide the hostname-IP mapping. Each DNS reply message carries one or more resource records.

A ==resource record is a four length tuple== that contains the following fields: `(Name, Value, Type, TTL)`

- TTL: Is the time-to-live field; it determines when a resource should be removed from a cache.

|Type|Name|Value|
|:--:|--|--|
| `A`|hostname|IPv4 Address|
| `AAAA`|hostname|IPv6 Address|
| `NS` | Domain | hostname of Authoritative DNS Server |
| `CNAME` | alias hostname | Canonical hostname |
| `PTR` | IP Address | hostname |


#### Inserting records into DNS Database:

A registrar is a commercial entity that verifies the uniqueness of the domain name, enters the domain name into the DNS database, and collects a small fee for its services.

When we register a domainname with some registrat we also provide the name and the IP address of our primary and secondary authoritative DNS servers.

Registrar will add $\texttt{Type A}$ and $\texttt{Type NS}$ records in the TLD DNS servers.