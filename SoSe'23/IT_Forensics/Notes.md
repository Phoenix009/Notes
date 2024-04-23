# Foundations

**Evidence**:
Any items, documents and statements that are ==included in a legal investigation for the jury's or judge's consideration== in the determination of individuals guilt or innocence.

**Locard's Exchange Principle**:
Physical evidence cannot be wrong, it cannot perjure itself, it cannot be wholly absent. Only human failure to find it, study and understand it, can diminish its value.

**IT Forensics**:
It is the gathering and examining of information in computers, storage media and networks to be used in criminal investigation and/or as evidence in a court of law.

**Post Mortem Analysis**:
- Investigation of an incident after the fact.
- Mostly analysis of storage media for non-ephemeral leads of incidents.
- Focus on deleted, renamed, encrypted and hidden files.

**Live Forensics**:
- During the incident
- Secure and analyse ephemeral data
- Focus on RAM content, established network connections and running processes.

**Where do digital leads originate?**
Physical leads can be collected by examining :
- Magnetisation of hard disks
- Transistors state of charge in ephemeral memory
- EM wave in a cable

Digital leads are interpretation of physical leads. Tools are required for this interpretation. Several levels of interpretations are possible and misinterpretation is possible on each level. 

Example:
- Hard disk magnetisation: ==physical lead== $\longrightarrow$
- Interpretation of bits as a file by the file system $\longrightarrow$
- Interpretation of the file as a text file $\longrightarrow$
- Interpretation of the text file based on a certain character encoding: ==digital lead==

**Avoidable Digital Leads**: are usually conscious generation. ex: log files, timestamps in file systems

**Unavoidable Digital Leads**: leads whose generation cannot be prevented. Ex: old stack frames

==Unavoidable digital leads are interesting for IT forensics as manipulation is more difficult.==

Digital leads should usually be mapped to a person for it to be useful. This is simplified if some authentication mechanism is available.

Advantages of digital leads:
- Exact duplication is possible
- Hard to destroy

Drawbacks:
- Often huge amounts of data can be difficult to handle
- Experienced investigators are required to secure digital leads


# Data Collection

==Forensics process== provides a structure for any investigation:
1. Identification of evidence
2. Collection and Preservation of that evidence
3. Analysis of the collected evidence
4. Reporting based on documentation of the previous steps

*Goals of this process may be in conflict with goals of incident response.*


## Duplication
What should be duplicated and how is it done?

### 1. **Simple Duplication**: 
Simply copies specific data, single file, group of files, partition etc

### 2. **Forensic Duplication**:
Goal: Image every accessible bit from the source medium

#### Image formats for forensic duplication:
##### a. Complete Image:
- Duplicate every addressable unit on the storage medium.
- Used whenever possible

- Advantages:
	- most thorough option
	- allows finding hidden data in unallocated space

- Disadvantage:
	- Time consuming

##### b. Partition Image:
- Duplicates every addressable unit of a partition
- Used when excessively large disk or due to legal requirements

- Advantage:
	- low level analysis of partition possible
	- allows reconstruction of deleted files

- Disadvantage:
	- remainder of data is not captured

##### c. Logical Image:
- duplicates specific files with their metadata and integrity hashes
- Used due to legal constraints or duplication from a live server is required

- Advantage:
	- Allows duplication without taking the device down

- Disadvantage:
	- prevents any low-level analysis


##### Image Integrity:
Forensic images must include a cryptographic hash for
- detecting changes
- demonstrate forensic image is accurate

#### Performing Forensic Duplication:
- Traditional Imaging Process:
	- Performed on static devices after powering them off
	- boot suspect system unto forensic imaging environment
	- Duplication done using software: `dd`
	- Uses hardware/software write blockers to prevent modification to source medium

- Live Imaging Process
	- Performed on running devices
	- Additional care has to be taken to prevent modification
	- No write blockers can be used



#### Special Cases:
##### HPA - Host Protected Area:
- Hidden area at the end of the drive not accessible to the user, BIOS or OS
- Used for recovery 
- Can be used to hide illegal data
- **Detection**:
	- During boots automatically detected
	- manually using 
		- `hdparm -I`: returns number of sectors with drive specification
		- `disk_stat`

##### DCO - Disk Configuration Overlay:
- Used for reducing the number of sectors shown to the BIOS
- Detection: using `hdparm --dco-identify`

##### VM - Virtual Machines:
- Easy to image/duplicate

##### RAID - Redundant Array of Inexpensive Disks:
- Option 1:
	- Boot system into trusted environment
	- Duplicate using RAID controller

- Option 2:
	- create image of physical disks
	- reconstruct RAID
	- harder




# File System Analysis:
- Why learn about File Systems?
	- Working provides insights for investigation,
	- deleted files can be recovered,
	- detection of anomalies, etc

- Terms:
	- Data Storage Block: 
		- Smallest addressable unit of a storage medium
	
	- File system Block:
		- Smallest addressable unit of a file system
		- aka. clusters
	
	- Logical Partition Address:
		- Address of the block relative to the beginning of partition
	  
	- Logical File System Address:
		- Address of the block relative to the beginning of File system
	  
	- Logical File Address:
		-  Address of the data block relative to the beginning of the file

## Carriers File System Reference Model:
### File System Data:
- Information about the organisation of file system
- Information about:
	- FS block length
	- Size of FS, etc

### Content Data:
- Actual content of the file system
- Stored in file system blocks

### Meta Data:
- Contains information about the data
- Information about:
	- location of contents
	- size
	- timestamps (access, creation, metadata)

### File Name:
- Maps file names to the INodes

### Application Data:
- used for additional useful features like journal, stats, etc




## Analysis:
### For Content data:
#### Background:
- Creation of File:
	- Data blocks are allocated
	- allocated blocks are marked assigned by FS
	- allocation strategy depends on OS and FS
	  
- Deletion of a File:
	- Often only meta data deleted / marked as invalid
	- data blocks with file content remain unchanged
	- can be overwritten later
	  
- Slack:
	- File Slack:
		Memory area between EOF and end of the final allocated block
		
	- RAM Slack: 
		Memory area between EOF and end of the current storage block
		
	- Drive Slack:
		Unused data storage blocks within last file system block associated to a file


#### Search using meta data:
- assumes availability of meta data like references to the data blocks
- Data blocks can be read into a new file for analysis

#### String Search:
- Search for keywords
- Can also be done on unallocated blocks
- Challenges:
	- Data fragmentation: data spans blocks
	- difficult to associate data to users
	- Encoding makes search difficult


#### Search using Hash Value:
- Assumes availability of the content but location not known
- location can be found using
	- Hash the content
	- Hash all the files in FS
	- Match hashes


#### Search using part of a file
- Assumes availability of a part of a file whose location is not known
- location can be found using:
	- Hash the known block
	- hash of all blocks of the image
	- match the hashes
- Use rolling hash functions to avoid problem of fixed block boundaries

### For Meta data:
- Evaluation of timestamps:
	- Chronological analysis of access and modification timestamps
	- provides important clues for the actions of the attacker
	- ==Caution==: could be manipulated
	  
- Evaluation of data blocks:
	- Write data blocks into a new file for analysis


### For File Names:
- File deletion strategies allow for reconstruction of deleted files
- File name and reference to meta data are preserved as they are simply marked "unused"

### For Application data:
- Ex: For journalling file systems, the journal can be used for analysis







## NTFS - New Technologies File System:

- Design Principles:
	- Reliability
	- Security
	- Support for large storage media
	- Scalability

- Journalling File System:
	- all operations are logged in a journal
	- Allows recovery after failure
	- $LogFile: journal file for meta data
	- $UsnJrnl: journal for recording changes to volume

### NTFS Components
#### NTFS Boot Sector:
- Information about layout of volume
- Information about structure of FS
- Boot Code

#### MFT - Master File Table:
- Information necessary to retrieve files

#### File System Data:
- Additional data about FS not included in the MFT

#### MFT Copy:
- For recovery




### MFT - Master File Table:
- Is the heart of NTFS: Every file has one entry, even MFT itself
- Consists of MFT entries/records
- Initially has 16 entries for meta data and FS related files
- NTFS reserves 12.5% of the volume for MFT

#### MFT Record - one for each file:
- **Header**:
	- 42B
	- Structured, 12 fields
	  
- **Body**:
	- 982B
	- Unstructured
	- Contains of MFT attributes - attributes of each file

#### MFT Attributes:
- **Header**:
	- 16B
	- Attribute Identifies
	- Size of Attribute
- **Body**:
	- Size defined by Header
	- Contains content of the attribute
	- Resident attribute: content stored in MFT record
	- Non-Resident attribute: stores addresses of the cluster in which attribute content is stored

##### $STANDARD_INFORMATION:
- ID: 16
- Resident
- Contains file owner, timestamps, flags etc
- timestamps can be easily modified

##### $FILE_NAME:
- ID: 48
- Resident
- Contains file name, parent directory, timestamps
- timestamps modification required raw disk access
- Workaround: ==double time stomping==:
- Insight: When moving a file, FS copies the $STANDARD_INFO timestamps to $FILE_NAME and then updates the $STANDARD_INFO timestamp.

##### $DATA:
- ID: 128
- Contains file contents
- Resident for small files (<= 700B)
- Non resident for large files.
- the data run lists are defined after 32B of the $DATA headers
- 2B give the offset from the HEADER for the 1st entry of the list




### File System Meta Data Files:
- Used for storing file system data
- First 16 entries in MFT are of meta data files

List of Meta data files:

| | |
|-|-|
| $MFT | Record for the MFT itself|
| $MFTMirr | Backup copy of the first MFT entries |
| $LogFile | Journal of meta data changes |
| $Volume | contains information on identifies and version of volume |
| $AttrDef | Provides mapping from ID to names of the attributes |
|. | Root Directory of the system |
| $Bitmap | allocation status of all clusters in FS |
| $Boot | Boot sector. contains info about FS, starts in cluster 0 |






# Collection of Ephemeral Data:
- **Live Forensics**:
	- Preservation of ephemeral data for later analysis
	- *Precondition*: The system is still running
	- Modification to the system state should be minimised

- **When to perform Live Forensics?**
	- Ephemeral data is crucial and not available elsewhere
	- Removing power source might destroy the evidence
	- We have to always be conscious of the impact of decision on the integrity of the evidence

- **How to minimise risk?**
	- Have we tested the live response process on similar system?
	- Is the system particularly sensitive to performance issues?
	- If the system crashes what is the impact?
	- Do we have the approval of the stakeholders?

## Getting Access:
Physical access can be sufficient for preservation of main memory.

### DMA Attack:
- Use of DMA by connected devices for access to main memory
- bypasses the OS thus access control of OS has no effect
- Possibility of reading the entire RAM using an external device
- Mitigation of attack using IOMMU - Input output memory management unit

### Cold Boot Attack:
- Circumvent OS when accessing RAM content by booting a different OS or by removing RAM modules
- DRAM modules need to be refreshed periodically to maintain information
- Cooling of DRAM modules slows down the decay rate
- Two options:
	- Perform a cold reboot and copy the contents shortly afterwards
	- Cool down the memory modules and plug them into another machine



## What to collect?
- Memory:
	- Main Memory content
	- Clipboard contents
	- Ephemeral FS
	  
- Network:
	- Network interface details, IP and MAC address
	- Routing table, ARP cache, DNS cache
	- Network connections and open ports
	  
- System Information:
	- System configuration data
	- Environment variables
	- Kernel stats
	
- Processes:
	- Processes including parent processes and runtime
	- open files and handles
	- associated network connections
	  
- Users:
	- List of local user accounts and group membership
	- User login history, including user name, source and duration
	  
- Storage:
	- Standard system log data
	- Appropriate application log data
	- Full system listing, including appropriate timestamps for the FS

## How to collect?
Two approaches:
	1. Manual Collection
	2. Automated Collection:
		- Preferred
		- Reduces risk of human error
		- Faster

- Preparation:
	- Practice all actions on a test system
	- Simulate problems
- Documentation:
	- Document what was done and when
- Tools:
	- minimise impact on the target system
	- tools should keep a log including checksums
	- automate as much as possible
- Data:
	- is collected in the order of volatility
	- treats data as evidence



# Encryption and Passwords:
## Common occurrences of encrypted data:
- Data in Transit
	- Virtual Private Networks (VPNs)
	- TLS - Transport Layer Security
	- Application layer encryption
	  
- Data at rest
	- Encrypted hard disks and other storage media
	- Encryption within file system
	- Encryption by application

## Cracking Passwords:
### Storing passwords:
- Passwords are normally stored as hash
- Forensic investigator may have access to hashed passwords
- On its own not very useful for digital forensic

### Password Cracking:
Assuming the Live Forensics has captured the live image and has the hash of the keys/password.

#### Brute Force Attacks:
- Test all possible passwords up to a certain length and assuming a set of constraints
- Calculate the hash value of the password and match
- Easy to implement
- Very long runtime

#### Pre-computation:
- Pre-calculates the hash values of all possible passwords and stores in a look-up table
- Very large storage space required

#### Using Hash Chains:
- Creates long hash chains using:
	- $h(p)$: hash function maps a password to a hash value
	- $r(p^\prime)$: reduction function maps a hash value to a password
- $$p_0 \rightarrow h(p_0) \rightarrow v_1 \rightarrow r(v_1) \rightarrow p_1 \rightarrow \cdots v_n$$
- We only store the $(p_0 , v_n)$
- Testing a value $v_k$:
	- Compute the reduction and hash function
	- If result equal to $p_0$ or $v_n$ of any chain, we have found the chain
	- Take $p_0$ perform computation until finding $v_k$
	- Password found


#### Rainbow Tables:
- Same concept as a hash chain




# Collection of Application/Service Data:

## Application: Antivirus:
- Purpose:
	Detection of presence and prevention of execution of malicious software

- Concept:
	- Quarantine as usual threat removal mechanism
	- file identified as threat is encoded and moved to a designated location to prevent execution
	- immediate deletion of possibly malicious files

- Significance for Forensics:
	- Antivirus software has verbose log files which might contain evidence of malicious activity
	- malicious software used by attacker might be preserved in quarantine

- Symantec Endpoint Protection:
	- Log Files:
		- File name scheme: MMDDYYYY
		- plain text comma delimited logs
	- Quarantine:
		- two .vbn files for each quarantined file:
			1. contains metadata
			2. encoded representation of quarantined file

- McAfee virus scan:
	- Log Files:
	- Quarantine:
		- one .bup container file for each quarantined file
		- metadata part XOR 0x6A
		- quarantined file XOR 0x6A



## Web Servers:
- Purpose:
	 store, process and deliver websites to clients

- Concept:
	- Communication between client and server via HTTP
	- Server listens on TCP port 80 for HTTP and on port 443 for HTTPS traffic
  
- Significance for Forensics:
	- Web servers often target of attacks
	- Two important sources of evidence
		- Web server logs: contain evidence of attack
		- Web server content: contains additional or modified files, scripts etc

- Challenges:
	- Virtual Hosting:
		- one server hosts many websites
		- We have to be careful to avoid interruptions to unrelated websites
		- Usually individual log files for different virtual hosts
	- Load Balancing:
		- Central server might be used as a proxy for a number of actual web servers in backend
		- Correlate activity from load balancer and web server logs
		- Solution: 
			- Configure load balancer to pass through details
			- Log `X-Forwarded-For` field

- Apache HTTP server:
	- Configuration:
		- Need to understand configuration to set up logging
		- main config file in `/etc/apache`: `httpd.conf`/`apache.conf`/`apache2.conf`
	- Content:
		- default: `/var/www/`
		- need to check configuration directives: `ServerRoot`, `DocumentRoot`
	- Log files:
		- in `/var/log/(httpd/apache/apache2)`: `access.log`, `error.log`
		- Format of log entries specified by `LogFormat` directive



## Database Servers:
- Purpose:
	- backend of many applications
	- maintain collection of data in organised manner
	- allow queries of the data collection

- Significance for Forensics:
	- db holds critical information
	- client connection logs: Did the attacker connect to the db
	- error logs: help to uncover brute force attacks
	- query logs: allows to identify what the attacker has done
	- database storage: contains content of the db

- MySQL:
	- Preparation:
		- only error log enabled by default
		- logging of connections and queries require enabling
	- Log Files: common location `/var/log/mysql`
	- Database storage:
		- common location: `/var/lib/mysql`
		- ideally shutdown and create full forensic backup





# Network Monitoring:
## Network Infrastructure Services:
### DHCP - Dynamic Host Configuration Protocol:
- DHCP server in network assigns a IP address to devices
- Helps the device for setting up
	- default gateway router,
	- DNS server,
	- NTP server, etc

- Concept:
	- client broadcasts DISCOVERY
	- server responds with unicast OFFER
	- client requests a REQ
	- server ACKs, sends lease duration and other config information

- A particular IPAddress can be assigned to different devices at different times, DHCP logs must be used to find device assigned a suspect IPAddress at a specific time.


### DNS - Domain Name Server:
- Purpose:
	- Resolution of domain names to their IPAddress

- Concept:
	- Uses hierarchical system
	- Recursive and Iterative resolution
	- Caching of responses

- Usually a centralised DNS server in enterprise network
	- allows for logging queries
	- allows identification of affected devices
	- allows identification of attack domains used


## Network Monitoring:
- Purpose:
	- Allows collection of more logs
	- Helps to reinforce findings form application logs. Victim machine logs could be manipulated

### Level of details for network monitoring:
#### 1. Header and Full Packet Logging:
- Full packet logging or
- Header capture: ignores payload, logs first 64B
- Collects communication information that flows through the network

#### 2. Event based alert monitoring:
- Sensors generate events based on indicators
- typically done using NIDS - Network Intrusion Detection System

#### 3. Statistical Modelling:
- High level view on connections / flows that traverse the network
- Parse protocol headers from a source over a period of time
- used when strange activity occurs but no explanation so far



### Setting up network monitoring system:
- Setup affected by many factors:
	- Network Architecture
	- Bandwidth
	- Budget
	- Legal Requirements

- Steps to perform:
	- Define the goals of the network surveillance
	- Ensure proper legal standing
	- ensure security of platforms

- Alternatives:
	- Use commercial solutions: Good but expensive
	- use homegrown solutions: Cheap but difficult

- Deploying Network Servers:
	- Identify appropriate positions in the network
		- Where are the egress points?
		- Where are the NAT proxies?
		- are choke-points available?
	- Get traffic to be monitored into the sensor:
		- Use span ports of routers
	- Secure the sensors:
		- Restrict physical and logical access to the sensors
	- Evaluate:
		- is the monitoring receiving the traffic that is intended to be monitored?



