The books treats this subject from the perspective of an organisation that buys third-party software.

The ultimate goal of service requirements is to find the middle group between what the customers ideally wants, what is technically possible, what is financially affordable and what can be achieved by the date that the solution needs to be available.

# Services make the Environment:
The richness and utility of the environment we provide to our users depends on the quantity and quality of services we provide.

==**Foundational Services** create the platform that other services rely on.==
Ex: DNS, DHCP, network access and internet gateways.

==**Basic Services** are user visible application that most people have come to expect in an organisation.== 
Ex: Printing, email, file storage etc.

Because of their visibility and pervasiveness people have developed expectations that these services will be reliable and always available.

==Lastly there are **primary applications** and the **back-office systems** that supports them.== 

Applications drive business functions such as payroll, inventory management and so on.
Back office systems include the database and the other behind the scenes services that support applications. 


# Starting with a Kick Off Meeting:
Kick off meeting should have all the stake holders present.

==**Stakeholders** are all the key people affected by or involved in the product==

Goals is to get agreement on the following things
- Goal of the service
- Timeline for completion
- Approximate Budget


# Gathering written requirements:
Requirements are a list of what the service will be able to do.

The list of requirements guides all the other steps: design, engineering, implementation, testing, and so on.

There are different kinds of requirements:
1. Customer Requirements: features and functionality of the service
2. Operation Requirements: what is needed to make the system work in the organisations IT infrastructure and be maintainable.
3. There could be other requirements like budget requirements, security requirements etc

Written down requirements have various benefits:
- Transparency: everyone can read the requirements document.

- Fewer misunderstandings: Seeing the decision in writing verifies that everyone is agreeing to the same thing.

- Buy-in and approval: Written requirements provide the ability to get buy-in and management approval in an accountable way. 

- Fixed scope: A formal requirements document provides a mechanism for handling additional feature requests that arrive after the agreement has been reached, preventing feature-creep.

- Accountability: Accountability is a two-way street. Customers can point to the document when a requirement you’ve agreed to turns out to be missing or incomplete. Documentation of requirements also helps delineate features versus bugs. A bug is a requirement that is incorrectly implemented. A feature request is a new requirement that wasn’t previously agreed to.


# Customer Requirements:
Services are built for customers. If a service does not meet the customer needs, building the service was a wasted effort. 

## Describing features:
- Requirements should focus on the list of features stated from the perspective of what the customers should be able to accomplish.
- Phrasing requirements at the right level of abstraction is one way that we can prevent problems.

## Questions to ask:
- How do customers intend to use the new service? 
- Why do they need each feature? Where, when, and how will the system be accessed? 
- How critical is the service to them, and which levels of availability and support do they need for the service?
- “As a \<type of user>, I want \<some goal> so that \<some reason>.”

## SLAs
- enumerates the services that will be provided and the level of support they receive.
- Should document the customers needs and set realistic goals for the SA team for features, availability, performance and support.
- Always discussed and agreed upon by both parties.

## Handling Difficult Requests:
- Rather than say no focus on the need not the technology
- Few minutes to educate the customers is well worth the effort

## Scope, Schedule and Resources:
- a project plan has three elements: 
	- scope (the features)
	- schedule (deadline)
	- resources (budget and staffing)
- Get buy in from management as to which of these elements are flexible and which are inflexible at the start of the project. 
- the buy in process helps communicate to all stakeholders, prevents a lot of confusion all around and helps focus people on the right priorities.

# Operational Requirements:
Operational requirements are the things that make a system maintainable. These are the features required if we are to have a system that is reliable, scalable, and secure

## System Observability:
- it must be observable or we cannot man- age it, fix it, or scale it.
- We must also have situational awareness to reason about it, predict its future behaviour and do capacity planning.
- Logging: All parts of the system should log events, transitions, and operations
- Monitoring: It should be possible to monitor not just whether the system is up or down, but also internal resources and timing

## Remote and Central Management:
- Remote management permits servers to be located anywhere
- Services that are replicated in many places must be manageable in a centralised fashion.

## Scaling up or out:
- Services generally scale in one of two ways: up or out.
- Scale-up means getting a bigger system.
- Scale-out means the system is expanded by adding more replicas

## Software Upgrades:
- Ideally, it should be possible to test a new software release outside of production and upgrade the service without interrupting it.
- It should be an operational requirement that the service is designed in a way such that it is possible to deploy the service to a test environment for the purpose of both testing the upgrade process and testing the new release itself.

## Environment Fit:
- The better a service fits into the existing IT environment, the easier it is to adopt it in your environment.
- For a small project, it is reasonable to make it an operational requirement that the service fit into the existing infrastructure

## Support Model:
- A key component in the success of a new service is support processes.
- The project plan will need to include time and budget resources to implement the answers to these questions
- Documentation may need to be written, new staff hired, training completed, helpdesk operations updated, monitoring systems expanded, and so on.

## Service Requests:

## Disaster Recovery:
- Acting like everything will always function perfectly is irrational. Instead, we need to prepare for failures so that we know how to recover from them.
- The most common data restore requirement is for something that was deleted by accident. As a result, the operational requirement of restoring individual data points has been implemented in most systems, often with no SA intervention required.



# Open Architecture:
- Wherever possible, a new service should be built around an architecture that uses open standards and open protocols. This means protocols, file formats, and APIs that are publicly documented so that others can write to those standards and make interoperable products without having to worry about royalties or patent restrictions.
- By contrast, a closed service uses standards, protocols, APIs, and file formats that are owned by one company, are controlled by that one company, and do not interoperate with other products.