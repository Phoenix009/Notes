1st way: 
	Use CI/CD which create the ability to rapidly and confidently launch new releases into production.

2nd way:
	first develop a MVP and then enhance it with many small improvements over time

The six step towards launch has many staging areas through which out software releases flow through.
- development
- testing
- beta
- production

This flow is used throughout industry but is non optimal. 

A more modern technique is called continuous integration and continuous delivery which automates software building, packaging and testing so that it can be done frequently.

Each release is automatically installed in a stage and put through a testing process. In some environments these tests gate automatic deployment to the production environment.

## Test ordering:
- The order in which tests run is important.
- The more expensive tests should be placed at the end of the testing pipeline. A less expensive test failing early prevents us from having wasted resources on a more expensive test.
- Sites with fully automated testing can achieve truly continuous integration and delivery. that is each change triggers a release that is built and tests. This way if there is a failure, team members know exactly which change introduced the problem
## Launch Categorisation:
### Chaotic:
- Releases are brought into testing at random times. 
- The building, deployment and testing of a release are not automated an may be ad hoc.

### Periodic:
- Releases are done on a schedule
- Usually, building and deployment are fully automated. Testing is generally automated with a few expectations that are done manually.

### Continuous:
- Each change to the source triggers an automatic build, testing and deployment.
- Because these happens so frequently they must be automated.
- Actual release to the production stage requires human approval.

### Push-on-green:
- Same as continuous
- However, push to production is approved when the status of all tests is green

### From Chaotic to Push-on-Green:
- It is difficult for a chaotic project to jump all the way to push-on-green. 
- Instead, one generally seeks to improve a project one level at a time. 
- A reasonable goal is for all projects to aspire to periodic or continuous launches. 
- The extra investment required to get to push-on-green should be reserved for only the most important projects. 
- Push-on-green requires an extremely high level of confidence in your automated testing procedure that can be achieved only by involving everyone from product management to developers to operations.

[[Making Push on Green a Reality]]

# Minimum Viable Product:
Another way to organise our work is to first build a beta system with the absolute minimum features.

We launch the MVP into the beta staging are. Then we add new features one at a time.

When the deadline arrives we launch with the features that are working in the beta.

Because we will be launching in the beta so many times, it is important that the end-to-end system from building the pieces from the source files, to deployment are fully automated.

The MVP approach gives us a different paradigm for launching services.

## Features drive the launch:
- We launch when the features are ready.
- If we carefully choose the order in which we add features, we could launch earlier for certain segments of our target population.

## We are always ready to launch:
- What gets launched may not have every feature, but a system like this is never actually done.

## Launching is low risk:
- Because we have launch to beta multiple times we get good at it. the process is automated.
- Process gets smoother the more we use it.

## Different between beta and production stays small:
- Since we are adding one or two features at a time, the delta between beta and production versions stays small.
- We can concisely communication what is in each release, which makes our customers better informed and better able to understand our work.


# Rapid Release with Packaged Software:
This section explains how to apply rapid release methods with externally produced software.

Industry trend is toward more frequent releases. Competition with web based application is driving packaged software to participate in trend for frequent releases.

Our rapid release examples so far have been with home grown software. Now we see how to deal with third party packages.

## Testing before deployment:

*Do you think testing of third party softwares is required?*

- Testing requires building en environment that replicates the existing system, then performing the upgrade, then conducting various tests.
- This is all very much the same as the testing process for home grown software.
- However many organisations do no testing at all and simply assume that the vendors testing is sufficient
- This misses the fact that the vendors environment cannot possible include the infrastructure we have built around the vendors product.

As a professional system administrator, it is your responsibility to use resources wisely. 
On the one hand, it would be irresponsible to set up a fully automated test infrastructure for a inconsequential service that is updated rarely. 
On the other hand, it would be equally irresponsible to not set up a repeatable testing structure, automated or manual, for the company’s most critical system.

## Time to deployment metrics:
A key metric to track is the amount of time between when an update is available and when it is in use in the production.

the longer the time span, the more we are vulnerable to security holes and bugs.

If we installed every update immediately, however we would be the first to discover problems.

WE can seek a happy medium between these tow extremes. An alternative method is to have a testing framework that lets us gain confidence in the updates by testing them ourselves.

[[Better Software Upgrades]]

# Cloning Production Environment:


# Example: DNS/DHCP Infrastructure Software:

## First Milestone:
The test automation tests not only features but also the upgrade process itself.

The tests are run on a fresh installation as well as an older installation that has been upgraded to the most recent release.


# Launch with Data Migration:
Most of the problems are related to insufficient resources, process taking longer than expected, bugs and general unpreparedness.


# Controlling Self-Updating Software:
Many software packages automatically update themselves

Self updating software is a blessing, as the upgrades happen automatically until an upgrade breaks.

Because of this IT department can choose to tightly control when applications are automatically updated.

the ability to control updates is usually considered an enterprise feature. It enables companies to test new release before they are distributed to the entire company.

