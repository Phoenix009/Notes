The chapter is about launching a new service such as an email system. 

The way that a new service is launches is every bit as important as the way that it is designed

the customers first experience with the service will color the way that they view the service in the future.
# Planning for Problems:
The road to launching a service is full of tricks and traps that catch us in the most non-obvious ways: 
- Users with a particular web browser can’t access it, 
- the service doesn’t work for remote users, 
- the production environment doesn’t have enough space, 
- the documentation is confusing, and so on.

It is irrational to expect a flawless launch.
This chapter is about the work we must do to make the reality match out optimism

The difference between a problem and a crisis is preparation.

# Six Step Launch Process:

## 1. Define the Ready List:
This is the list that tells us when we are done and ready to launch.
### What to List?
#### Must Have Feature Set:
- Launch cannot happen without these features.
#### Would be nice feature set:
- all feature that are not must haves fit in this category.
#### Bugs to be fixed:
- Testing reveals bugs that MUST be tracked.
#### Assertions and Approvals:
- This is the list of things that must be true before launch.


### List Attributes:
There’s a S.M.A.R.T. Way to Write Management’s Goals and Objectives.” One is in a better position when goals are specific, measurable, achievable, relevant, and time-bound:
- Specific: The goal addresses a specific area for improvement.  
- Measurable: The goal’s success (or progress) can be quantified.  
- Achievable: It can be realistically achieved given available resources.
- Relevant: It is worthwhile, and in line with broader goals.  
- Time-bound: The goal has a deadline.

At a minimum each item in the list should have:
- brief title, 
- a priority, 
- the name of who is responsible for completing the task, 
- a link to the history of the task, and
- a status.


### Storing the List:
The list should be viewable to all stakeholders. 

Following are the most common places to store and maintain the list:

#### Bug or ticket system with tags:
One method is to assign a bug ID or ticket to each item in the list and label the items in a way that one can easily query for all the items.

#### Bug or Ticket System with watchlists:
Some issue tracking systems permit one to create a “watchlist” or “hotlist” of items, which provides similar functionality to tagging

#### Kanban Board:
Cards are placed in columns: 
- a “backlog” column for issues that aren’t being worked on yet, 
- an “active” column for issues that are in progress, 
- and a “completed” column for items that are finished. 

The history of the item is stored on each card, and may include a URL to a bug or ticket if needed.

#### Online or shared spreadsheet:
- One can list each item in a row of a spreadsheet, using columns for attributes discussed before.
- Can be viewed by anyone and can be updated with appropriate permissions.


## 2. Work the List:
Make sure that everyone knows what is expected of them and their deadlines

Keep people on track

## 3. Launch the Beta Service:
Before launching to real customers, the service should be launched to an isolated environment for testing. ==The isolated environment is called a **staging area**.==

There are many different types of staging areas. Each has a different name and a very specific purpose.
1. **Dev**: Where developers can deploy to at any time for whatever testing purpose they have.
2. **Quality Assurance**: The QA process tests the release and either accepts or rejects it, and provides a list of bugs found.
3. **User Acceptance Testing**: The UAT is a sandbox for external customers to test a release.
4. **Beta or Early Access**: The Beta area is where approved customers have early access to a release so that they may provide feedback or report bugs.
5. **Production**: This is where live users use the service.

Each stage has an entry and exit criteria. The entry criteria determine when the testing starts. The exit criteria restrict or gate whether the release passes or fails.
The larger the project the more important it is that these exit and entry criteria be explicit and written.

## 4. Launch the Product Service:
Launching is both a technical task and a communication task. The technical task involves pushing code to the production environment and any other step required to expose the new release to the users.

We need to communicate that the launch is happening what is new since the last release and most importantly whether any items are incomplete

You should never launch to all the users at once. release to waves of users, such as different departments or teams, starting with the most risk tolerant and ending with the most risk averse.


## 5. Capture the lessons learned:
A retrospective is a live meeting where the launch process is reviewed. the purpose of the retrospective are fourfold:
1. to get agreement about needed improvements from the project members
2. To educate the entire team about issues visible to only a few
3. To communicate problems to management, particularly ones large enough that their resolutions will require additional resources.
4. to spread what you learned to the entire organisation so all can benefit.

Retrospective is used to compile a document collaboratively. this document should have the following sections
1. Executive Summary: Lists down things that went well and what should be improved in the future
2. Timeline: high level summary of when the project started, when ti ended and major milestones in between.
3. Successes: What went well.
4. Failures: What did not go well.
5. Short term changes: What should be done differently. something that can be achieved simply.
6. Long Term changes: Changes that are large enough that making change may require the creation of a project.

## 6. Repeat:
There will be new releases with new features, bug fixes and so on. Each requires another launch.
Each launch should have its own ready list and use a similar process.
# LRR - Launch Readiness Review:
an LRR is a process created to assure successful launches by comparing the state of the service against launch readiness criteria LRC

## Launch Readiness Criteria:
LRC includes basic tasks such as 
- verifying that stakeholders have approved the launch, 
- that there is a disaster recovery plan in place, 
- the data backups are working and have been tests, 
- and that various security audits have been completed and so on.

When all the criteria are met, we are ready to launch. Management should be strict in applying criteria. If the exceptions are permitted, the business risk should be quantified so that managers understand the implications of what they are agreeing to.


## Sample Launch Criteria: 
//todo

## Organisational Learning:
LRC is a way to capture learning in an organisationally beneficial way.

Problems identified during retrospective become candidates to add to the launch readiness checklist.

Without an LRC process each team will probably make same mistakes with its launches.

## LRC Maintenance:
as LRC list grows it becomes a bureaucratic burden. We can prevent that problem by being cautious about adding new items and removing obsolete items.

The best way to remove something from the list is to make it obsolete because it has become automatic.

# Launch Calendar:
Large organisations that engage in many simultaneous launches require some kind of coordination of these activities. Usually this takes form of a launch calendar.

The calendar creates awareness of future launches. Having a shared launch calendar prevents conflicts. It also makes it easier to schedule isolated launches.


# Common Launch Problems:
## Processes Fail in Production:
Deployment processes that worked in other stages may fail in production.
EXample: Latency cascades

## Unexpected Access Methods:
Many launches fail because tests were performed using one access method but customers actually access it another way.

## Production Resources Unavailable:
Assumptions are typically made about resources available in production. 
Suppose before launch the developers announce that the new service will require a large amount of disk space, CPU, or bandwidth resources.
Developers simply assumed that these resources would be available without making their requirements known ahead of time.

The lesson here is to involve operations with resource and capacity planning early in development.


## New Technology Failures:
The first time  a new technology is used, the launch takes longer.

New technologies require weeks and possibly months of operation work before they can be used in production. this factor should be weighed as part of the decision to adopt the new technology.

## Lack of User Training:
For most new services, users require train- ing or at least simple screenshots that walk them through the steps to access the system.

Train- ing requires that the system be launched, but the launch can’t be done until the training is complete. The organisation, therefore, decides to launch without train- ing and ends up in a sticky situation where people can’t go back to the old system (it has been disabled) and don’t know how to use the new system.

One solution is to build a staging area used exclusively for training purposes. This provides a platform for training without disrupting the legacy production service.



