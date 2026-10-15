---
title: "Extreme Programming Practices"
weight: 2
bookToc: true
---

# Extreme Programming Practices

## Customer as a team member

It is desirable for the customer and developers to work closely together, sitting face to face in the same space.
Working in the same space lets each side understand the other's problems and work together toward solving them.
This literally refers to physical distance: it is most ideal for the customer and developers to work in the same room.
The greater the distance, the harder it becomes to truly incorporate the customer into the team.

## User stories

To work out a project plan, you need to know the outline of the "requirements" to some extent, but you do not need to understand them in detail.
At the planning stage, information sufficient to estimate the amount of work needed to implement the "requirements" is enough.

Of course, you should know that details exist, and you need a rough idea of what they are,
but you do not need to know the specific details.

The details of individual requirements change as the project progresses.
This becomes especially noticeable once the customer can receive a system that has taken coherent shape.
Seeing the early system running is what lets them truly focus on the requirements.
Therefore, **pinning down the specific details of requirements long before building them into the system is premature**.

## Short release cycles

**XP and Scrum have slightly different views on releases.** Here we introduce XP's view of releases.
Again, Fujitsu's public website happens to have a diagram introducing the process, so we quote it here.

![The XP process](../process.jpg)
Image source: [アジャイル開発とは（中編）(What is Agile Development? Part 2)](https://www.fujitsu.com/jp/group/fst/about/resources/featurestories/about-agile-02.html)

In XP, working software is released in short iterations of one week to one month.
The working software built during an iteration implements part of the sponsors' requirements,
and at the end of the iteration a demo is held to get feedback from stakeholders.

### Iteration plan

An iteration usually lasts about two weeks, and in each iteration several small features are implemented and delivered.
However, whether they will be included in the final product has not been decided.
The customer selects several user stories based on the estimates the developers have made, and those are what actually get implemented.

Developers estimate the amount of work for the next iteration based on the amount of work completed in the previous iteration.
The customer can select as many stories as they like, as long as they do not exceed that estimate.

Once the iteration starts, the developers break the stories down into tasks
and implement each task in the order that makes the most sense both technically and from a business perspective.

### Release plan

An XP team creates a plan that bundles roughly the next six iterations together.
This is called a **release plan**.
A release is usually set at three-month intervals,
and during that time iterations are repeated and the deliverables are bundled together and released.

In the [Iteration plan](#iteration-plan) section, I wrote that "whether they will be included in the final product has not been decided,"
but what goes into a release is decided at release time.
The customer can cancel or add stories, or change the priority of stories.

## Acceptance tests

Acceptance tests are also called E2E tests.
Strictly speaking, acceptance testing is a phase, and E2E testing is a way of carrying out acceptance testing.

The details of a user story are written in the form of "acceptance tests" presented by the customer.
A story's "acceptance tests" are created just before the story is implemented or in parallel with its implementation.
Usually, "acceptance tests" are written in some kind of scripting language so that they can be run automatically and repeatedly,
and by running the acceptance tests automatically and repeatedly, you can check that the system behaves as the customer requires.

There are many acceptance testing tools.
Well-known ones include Selenium, Puppeteer, TestCafe, and Cypress.io.
For API testing, there are Postman and its command-line tool Newman, as well as Karate, Pact, and others.
In the author's project, we adopted TestCafe for checking behavior accessed through a web browser,
and Postman and Newman for checking API behavior.
Tools have their own strengths and weaknesses, so it is a good idea to choose according to the characteristics of your project and application.

Also, **"acceptance test" scripts grow and evolve along with the system**.
When a new iteration starts and features are added, scripts are added on top of the existing "acceptance tests."
In other words, acceptance tests also serve as regression tests.

## Pair programming

Pair programming means programming in pairs of two.
Two people use one computer and work together to write code.
One member of the pair types the code, while the other checks the code for typos and looks for errors and possible improvements.
The pair engage intensely with each other, work while discussing as they go, and put their whole heart and soul into writing the software.

The roles in the pair switch frequently. When the person typing gets tired or stuck,
the partner offers advice such as "Wouldn't it be better to do it this way?", or takes over the keyboard and types the code in their place.

**In pair programming, the pair combinations change every day.**
This way, each programmer ends up being involved in all the features the iteration aims to build.
Knowledge of the system no longer becomes concentrated in one particular person.
One of the risk indicators a project has is the "bus factor."
It indicates "how many people would have to be hit by a bus for the project to fall apart."
If only one member understands the specifications of a particular feature, the project's bus factor is 1.
If that project member leaves the project for whatever reason, the project will collapse.
To prevent such a situation, it is best to always rotate pairs and spread the project's knowledge and know-how.

Pair programming also has the aspect of experts **teaching** other members,
and by repeating pair programming, you can expect to raise the development skills of the whole development team.

## Test-first development

Test-driven development (TDD) is covered in detail in the testing chapter, so here we only give an overview.

In test-driven development, **you first implement the behavior of the feature you are about to build as a unit test.**
At the point the unit test is implemented, the feature has not been implemented yet, so the test fails.
Then you develop in a flow of **implementing the feature for the purpose of making the failing test pass**.

The test code evolves almost simultaneously with the implementation code, staying slightly ahead of it.
As a result, a complete set of test code is built up together with the product code.

Whenever you make even a small change to the implementation code due to a specification change, you rerun the tests frequently to confirm that it works correctly.
**The unit tests serve as regression tests, letting you confirm that the change has not caused unexpected failures.**
Having a complete set of test code also has the benefit of making [refactoring](#refactoring) possible.

If you ask why tests are written first, it is in order to write a complete set of test code.
You might think, "Can't we write them afterwards?", but when tests are written afterwards,
there is a higher chance of gaps and omissions in the test code.
Then, when you do the refactoring described later, you may introduce unexpected bugs.
**Implement a failing test first, then write only the code that satisfies that test. This process is very important.**

## Collective ownership

A pair working together has the right to check out any module and improve it.
However, no individual developer holds responsibility for a module or technology.
Since everyone on the team ultimately ends up involved in developing the whole product, everyone shares the technology equally.

There are various ways to share code, but the author believes using Git is the most common
and the best suited to collaboration among multiple people.
Of course, changes may collide while sharing source code,
but you only need to resolve the conflicts each time and run the tests, so it should be easy to operate.

## Continuous integration

Developers work while checking in the code they are responsible for many times a day.
The rule is simple: whoever checks in first takes priority.
Whoever checks in later merges into that, and if changes conflict, the one merging later resolves the conflict.

XP does not block source code control.
In other words, anyone can freely check out a module at any time, modify it, finish testing it, and put it back into the main line (check in), without worrying about whether someone else has it checked out.

The larger the changes, the more likely they are to conflict.
Therefore, you must make a point of checking in frequently.

And to check in frequently, automating unit tests and acceptance tests becomes very important.

## Sustainable pace

Software development is not a sprint. It is a marathon.
A team that bursts off the starting line at full speed will burn out before the finish.
To finish quickly, a team needs to maintain its energy and focus at a sustainable pace.

**XP's rules strictly forbid overtime.** There is an important reason for this.
In the Scrum chapter, I explained that the amount of requirements a development team can deliver in a unit period is called velocity.
Velocity is the average of the story points completed in the last few sprints, and the plans for the following weeks are made based on this actual value.

What happens if you work overtime to finish the work here?
First, because you are working more hours than usual by the amount of overtime, the premise of velocity as the amount that can be delivered in a unit period breaks down.
Then the plans for the following weeks get made based on results produced with more hours than usual.
As a result, developers end up being required to push themselves even harder.
In other words, **the more you work overtime and push yourself, the more you are tightening the noose around your own neck**.

Also, agile development is generally carried out under time-and-materials (quasi-mandate) contracts. **Both under XP's rules and under the contract, you must never work overtime.**

## Open workspace

The team works together in an open room. Each pair is close enough to hear the other pairs' conversations,
so that they can notice when another pair is having trouble.
This way, everyone knows each other's situation, and developers can talk closely with each other at any time.

If you are fully remote, it is a good idea to provide **a channel where pairs can talk closely** and **a channel where the team can talk loosely**.
It is a good idea to introduce tools such as [zoom](https://zoom.us/) and [sneek](https://sneek.io/).
Both zoom and sneek are video chat tools, but **the areas of communication they cover are very different.**
zoom is suited to pairs talking while working closely together, and sneek is very effective for situations such as asking another team a quick question.
Both kinds of communication are important for moving a project forward, and you should adopt both.

For text-based communication, it is also important to adopt a chat-based tool such as [Slack](https://slack.com/) and strive to keep the cost of communication low.
For reference, here are [the Slack operating rules of the author's project](https://github.com/Onebase-Fujitsu/Operations/blob/main/slack_rules.md).

## Simple design

An XP team must do its best to make the software design as simple and expressive as possible.
It should also focus only on the stories planned for the current iteration and must not take into account stories that might be built in the future.
The design is changed each time to the one best suited to handling the current stories.

### Consider the simplest implementation

An XP team must seek the simplest possible way to handle the current set of stories.
For example, if the current stories can be implemented with a simple file system, there is no need to bring in a database.
**A simple implementation is also easy to remove later when it is no longer needed.**
Above all, aim for simple implementations.

### No "we will need it later"

Every application will scale sooner or later, and the time will come when it needs a DB or support for multiple users.
However, there is no need to prepare for that now.

A development team should consider adding such "we will need it later" implementations
**only when it can prove, or has very convincing evidence, that adding it now will save far more cost than waiting until it is needed.**

### Never do the same thing twice

The development team does not tolerate duplicated code. As soon as you find duplication, remove it on the spot.
There are various causes of code duplication, but the most common is copying and pasting code into multiple places.
When you find such places, create a base class or function to eliminate them.

If you find algorithms that are very similar but subtly different, it is a good idea to rewrite them as functions or use the Template Method pattern.
Whatever the reason, when you find duplication, stamp it out thoroughly.

## Refactoring

All code deteriorates.
As features are added one after another and bugs are fixed, the structure of the code gradually degrades, and if left alone, that deterioration produces an unmanageable mess.
The development team must prevent such deterioration by refactoring frequently.

Refactoring is the process of improving the structure of a system by making a series of small changes without changing the functionality itself.
Each individual change is trivial, but as they accumulate, they significantly change the system's design and architecture.

Whenever you change even a little source code, you immediately run the unit tests and refactor while confirming that nothing is broken.
Refactoring should be done frequently: at the end of the day, at the end of an iteration,
or even every hour.
Developers are required to keep the code clean and simple through frequent refactoring.

---

Finally, let's summarize XP.

{{% button relref="/docs/xp/conclude" %}}Summary{{% /button %}}
