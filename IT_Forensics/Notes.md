### Goals:
1. provide an overview of processes and methods for analysis and conservation of evidence after security incidents.
2. present selected technical tools for analysis and conservation of evidence
3. demonstrate the challenges for forensics associated with cryptographic techniques

**Evidence**:
Any items, documents and statements that are included in a legal investigation for the jury's or judge's consideration in the determination of individuals guilt or innocence.

**Locard's Exchange Principle**:
Physical evidence cannot be wrong, it cannot perjure itself, it cannot be wholly absent. Only human failure to find it, study and understand it, can diminish its value.


### Introduction:

#### IT Forensics:
It is the gathering and examining of information in computers, storage media and networks to be used in criminal investigation and/or as evidence in a court of law.

**Goal**:
Systematic investigation of criminal acts, usually aimed at gathering evidence admissible in court.


#### Post Mortem Analysis:
- Investigation of an incident after the fact.
- Mostly analysis of storage media for non-ephemeral leads of incidents.
- Focus on deleted, renamed encrypted and hidden files.

#### Live Forensics:
- During the incident
- Secure and analyse ephemeral data
- Focus on RAM content, established network connections and running processes.

### Where do digital leads originate?
Physical leads can be collected by examining :
- Magnetisation of hard disks
- Transistors state of charge in ephemeral memory
- EM wave in a cable

Digital leads are interpretation of physical leads. Tools are required for this interpretation. Several levels of interpretations are possible and misinterpretation is possible on each level. 

Digital leads can also be based on data that is either stored in systems or is transferred between systems.

Example:
- Hard disk magnetisation: ==physical lead== $\longrightarrow$
- Interpretation of bits as a file by the file system $\longrightarrow$
- Interpretation of the file as a text file $\longrightarrow$
- Interpretation of the text file based on a certain character encoding: ==digital lead==

Avoidable Digital Leads: are usually conscious generation. ex: log files, timestamps in file systems

Unavoidable digital leads: leads whose generation cannot be prevented. Ex: old stack frames

Unavoidable digital leads are interesting for IT forensics as manipulation is more difficult.

Digital leads should usually be mapped to a person for it to be useful. This is simplified if some authentication mechanism is available.

Advantages of digital leads:
- Exact duplication is possible
- Hard to destroy

Drawbacks:
- Often huge amounts of data can be difficult to handle
- Experienced investigators are required to secure digital leads


### Data Collection

==Forensics process== provides a structure for any investigation:
1. Identification of evidence
2. Collection and Preservation of that evidence
3. Analysis of the collected evidence
4. Reporting based on documentation of the previous steps

**Challenge**: We need to duplicate media for further investigation to avoid changes to the original.



Terms:

**Evidence**:
Any items, documents and statements that are included in a legal investigation for the jury's or judge's consideration in the determination of individuals guilt or innocence.

**Locard's Exchange Principle**:
Physical evidence cannot be wrong, it cannot perjure itself, it cannot be wholly absent. Only human failure to find it, study and understand it, can diminish its value.

Digital Leads:
	Adv:
		Hard to destroy
		Exact duplication possible
	DisAdv:
		Huge amounts of data
		Experienced investigator required
		