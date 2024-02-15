# DNS - Domain Name System
## [[DNS - Internets Directory Service#Introduction|What is  DNS?]]

## [[DNS - Internets Directory Service#Distributed Hierarchical Database|DNS Hierarchy]]

## DNS Resolution
![[DNS - Internets Directory Service#Resolution|DNS Resolution]]

## DNS Resource Records
![[DNS - Internets Directory Service#DNS Records and Messages]]



## DNS caching
![[DNS - Internets Directory Service#DNS Caching]]

This allows for security attacks that affect both privacy and security. Ex: DNS Snooping and DNS Poisoning


# DNS Cache Attacks:

## DNS Cache Snooping:
- Inspect the contents to see usage of a resolver
- Attacker Goal: Did a client recently visit domain X?
- Threat Model: Attacker and victim share same resolver
- Basic Idea: Attacker probes for cached entries
	- Attacker has to be careful to not set the DNS entries himself by setting the recursion resolution depth to 0. This way the queries that are not present in the cache are not resolved.
- Poses threats to security and privacy:
	- Find out which sites have been visited
	- Does the victim visit certain possibly illegal websites
	- Find out visiting patterns

## DNS Cache Poisoning:
- Attack Goal: inject attacker specified RRs to cache.
- Once the cache is poisoned, attacker can control the traffic to the domains of all clients that the use poisoned resolver. 
	- Redirect clients to malicious websites
	- Direct mail to mail servers under attack control
	- Disable Domains entirely (censorship)
### Out of Bailiwick responses:
- Threat Model: attacker can trigger name resolution and controls their own authoritative NS for arbitrary domain.

- Basic Idea: Inject records in response that point to arbitrary non related domains and hope that resolver will cache them.

In summary DNS caches are highly valuable targets:
- in band attacker can easily put any entries into cache
- out of band attacks are also feasible depending on the insecurity of the configuration of the resolvers
- In essence, DNS needs some for of security above adding entropy

# DNSSEC - DNS Security Extensions

- DNSSEc follows multiple goals:
	- Data Integrity
	- Origin authentication
	- Authenticated denial of existence
- Explicit non goals of DNSSEC:
	- no confidentiality
	- no protection against DDoS attacks
	- no protection on the path between client and resolver

- DNSSEC signes RRs
	- off the shelf public key crypto
	- attacker cannot spoof signed records
- Additional DNSSEC RR types:
	- `RRSIG`
		- contains signature for set of RRs in response
		- Group of RRs of same type and name
		- Precomputed at authoritative NS
		- Record defines type, algorithm, number of domain components TTL, signature in base64 format, etc
		- Recursive Resolvers needs to verify the signature. This requires the knowledge of used public key
		- Retrieve the `DNSKEY` from authoritative nameserver for the domain. This DNSKEY Record should again be signed to ensure the authenticity of the public key.
		- Thus resolvers need to verify the validity of public key
		- Retriever DS from authoritative nameserver for parent zone 
	
	- `DNSKEY` contains public key
	
	- `DS` certifies DNSKEY
	
	- `NSEC`/`NSEC3` for provable `NXDOMAIN` responses

DNSSEc replies on PKI as chain of trust:
- parent zone certifies keys of child zones
- if we trust the parent zone, we can verify keys
- if we do not trust the parent zone directly, traverse upwards to get parents parent 
- only root keys need to be known trust anchors which are hardcoded in software
- what happens if the root key gets compromised?
- What if an authoritative server is rogue or compromised?

DNSSEC validates top down
- to resolve any name, RR needs to know authoritative NS first. So we query the `NS` and `DS` records

DNSSEC for NXDOMAIN:
- DNSSEC can prove that a domain does not exist
- But there is no record/data to be signed if there is no RR.
- Solution NSEC (next secure record)

NSEC:
- Idea: order all domain names in a ring. IF NXDOMAIN, reply with NSEC that points to to existing domains before and after queries domain
- This allows an attacker to query iteratively and obtains all the domains that are present in the ring. Privacy issue

NSEC3:
- NSEC allows trivial zone enumeration. allows retrieval of all hosts in that zone
- NSEC3 solves this problem by hashing the names and returning hashes in response.
- Hashes are put into ring. NSEC3PARAM in parent zone controls how hashing happens (algorithm, salt)
- Dictionary attacks are still possible though.


# Routing
[[Routing Algorithms]]

- Routing is the process of transmitting packets from a source to a destination.
- Routers accept incoming packets and forwards them towards the destination on outgoing links
- Routing tables are used to find which router to forward the packet to.

[[Routing Algorithms#Inter-AS Routing BGP|What is BGP?]]

BGP Hijacking:
- Hijack by advertising false and malicious advertisements.

Route Filtering:
- Route advertisements are checked against access control lists
- Policies that an address space must be routed via X
- Internet Routing Registeries maintain ownership

S-BGP (Secure BGP):
- Add authentication and authorization capabilities
- PKI to authorize prefix ownership
- Prevent route advertisements from modifications
- Routing advertisements are encrypted
- Rarely used in practice :(

BGP Origin Validation through Route Origin Authorization:
Idea: Use PKI to authorise some AS to announce some prefix


# DOS - Denial of Service
- Attempt to disrupt a host service or network by exhausting its resources or crash a target.
- Resources typically targeted by DoS
	- Computational Resources: Overload target with expensive calculations
	- Memory: Consumes targets memory for the processing units
	- Bandwidth: Exhaust network link of the target

## DDoS - Distributed DoS:
- Attacker may control a botnet which is a network of malware infected systems to launch DDoS attacks
- Volunteers may synchronise to perform DDoS attacks

# Firewalls

## Why do we need firewalls?
- Sensitive networks are connected to Internet
- Heterogenous environment that is vulnerable to:
	- Software exploitation
	- reconnaissance attacks
	- data leakage

## Concept:
- Protects an organisation from internet
- all traffic between inside and outside passes a firewall
- only authorised traffic, as defined by policies may pass

# Simple Packet Filter Firewall
- Rules and policies regarding packet headers
	- SRC and DEST IP Address
	- SRC and DEST transport level address
	- IP Protocol field
	- Physical Interface

- Define ordered list of rules with default policy fallback
- Default = drop => white listing
- Default = accept => black listing

- Packet filters are simple and fast. They do not require any additional information except rules
- However, they have fundamental weakness
	- Decisions are per packet and stateless
	- Packet filters fail to identify related traffic

# Stateful Inspection Firewalls:
- Stateful firewalls record TCP/UDP connections
	- Maintain a directory of TCP/UDP connections
	- Specify rules based on packers and or connections

- Connection tracking allows for easier rules
	- Delete connections if they are closed
	- Connection States
		- `NEW`: packet starts new connection/stream
		- `ESTABLISHED`: packet is associated to existing connection
		- `RELATED`: packet starts a new connection associated to existing one

# IDS - Intrusion Detection System:

- Additional line of defence if intrusion prevention fails
- If intrusion detected quickly, take counter measures before damage is done or to limit the amount of damage
- enables connection of feedback data about attempted intrusion that can be used to strengthen the prevention mechanism
- honeypots are decoy systems that are designed to lure a potential attacker away from critical systems and collection information about attackers activities.

How do we detect an intrusion?
- Assumption of intrusion detection: behaviour of the attacker differs from that of legitimate users/processes/network activity
- However, there is an overlap in behaviour which causes a trade off between false positives causing higher number of false alarms

## Approaches to Intrusion Detection

### Statistical Anomaly Detection
- Collect data about users/process/network activity over a period.
- Statistical test to determine whether the observed behaviour is anomalous and a potential intruder.

- **threshold detection**: Define thresholds, independent of the user for the frequency of occurrence of various events.
- **Profile based**: profile the activity of each user is developed and used to detect changes in the behaviour of individual accounts or hosts

### Rule Based Detection:
- Defines a set of rules or attack signatures that can be used to decide that a given behaviour is that of an intruder
- Idea: rules for identifying known attacks or attacks that would exploit known weakness

