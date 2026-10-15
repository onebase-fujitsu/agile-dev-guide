---
title: "Artifacts"
weight: 3
bookToc: true
---

# Artifacts

The Scrum Guide defines three artifacts that the Scrum team should create:

* Product backlog
* Sprint backlog
* Increment

Let's look at each of them.

## Product backlog

**The product backlog is an ordered list of everything the product needs, such as the features, requirements, requests, and fixes you want to realize.**

![Product backlog](../tracker.jpg)

Let's look at a concrete example. This is the actual product backlog of a project the author is assigned to.

{{% hint info %}}
**Tip**  
The product backlog shown here as an example also serves as the sprint backlog, and its items are already broken down finely enough to work on.
{{% /hint %}}

How to size product backlog items is a matter on which projects and teams differ,
but you can roughly list them as feature A, feature B, and so on,
or break them down into units of work that take from a few hours to under a day, as in the author's project.
What matters is that it is consistent throughout the project.

This project manages its product backlog with [Pivotal Tracker](https://www.pivotaltracker.com/).
The service for managing the product backlog does not have to be Pivotal Tracker; you can use whatever you are used to.

Now, in the product backlog, as shown in the figure, you list what you want to realize and order it in the order you want to realize it.
Managing and keeping this up to date is the product owner's role.

Only items whose hypothesis testing is complete go into the product backlog.
In other words, features that have been confirmed as useful through interviews with end users are what appear here as requirements.

Product developers regularly estimate the amount of work for the requirements listed in the product backlog,
and then, without having to think about anything else, implement and release them as features in order from the top of the list.

## Sprint backlog

**The sprint backlog is the part of the product backlog to be done in one sprint, extracted and broken down into tasks.**
So how much work can a team do in one sprint?
First, let's think about estimating the amount of work.

### Estimating the amount of work

For estimating the amount of work, we use **story points**, a value that expresses the amount of work in relative terms, rather than absolute values such as money, time, or amount of code.

![Arrows](../arrows.jpg)
As an example, look at the two arrows drawn here.
If you are asked to "find the length of the lower arrow," it is quite a lot of work.
You would probably end up holding a ruler up to the screen to measure it.

But if you are asked to "find the length of the lower arrow compared with the upper arrow,"
anyone can come up with an estimate in an instant: "about three times as long"!
A precise estimate takes time to produce, but a relative comparison can be done in an instant. This speed is what matters!

The example above was about the length of lines, but estimating work is the same.
If asked "How many steps will implementing this feature take?", no one could answer on the spot.
But if asked "How hard is this feature compared with that one?", anyone can give an intuitive estimate.

In the 12 Principles of Agile Development,
there was a passage: "In agile, it is very important to **look for 'waste = things that do not create value' and have 'the courage to stop.'**"
In other words, the judgment is that a precise estimate might be useful, but if producing it takes an enormous amount of time, it is waste.
**No matter how accurate an estimate is, an estimate is only an estimate, and it does not create user value.**

If you are used to waterfall development, you might wonder, "Is such an imprecise, vague method really okay?"
However, each backlog item is only an amount of work that takes from a few hours to, at the very most, a day or two.
Being somewhat off has little impact. It is far more effective to cut the time spent on estimation and use that time to build the product instead.

So how should you decide the baseline amount of work?

This differs from team to team, but it is a good idea to make a simple task that takes a few hours the baseline of 1 point.
What matters is that the team agrees on the baseline amount of work and that it stays consistent throughout development.

### Sprint planning

We have estimated the amount of work for each work item. So where do we get the amount of work the team can do in one sprint?

The answer is derived from the average of past actual results.

![Sprint planning](../iteration.jpg)

Look at the product backlog again. There is the number "12" at the top of the screen.
This is called **velocity, the amount of requirements the team can deliver within a set period**.
It is derived from the average of the story points completed in the last few sprints, and it changes every iteration to reflect actual results.

{{% hint danger %}}
**Common misconception about velocity, part 1**  
People often ask whether velocity is productivity, but it is not.
Velocity is for a specific team in a specific context, and it is by no means standardized.
You cannot measure a team's productivity with velocity.
{{% /hint %}}

{{% hint danger %}}
**Common misconception about velocity, part 2**  
Velocity cannot be compared across multiple teams.
As mentioned above, velocity is for a specific team in a specific context,
so it cannot be compared across multiple teams. Do not forget that the amount of work used as the baseline for estimates differs from team to team in the first place.
Also, even within the same team, if members change, the context changes, so you cannot compare before and after.
{{% /hint %}}

Now let's get back to sprint planning. This team's velocity is 12, so it can complete 12 points' worth of tasks per week.
The week of 8/9 to 8/15 happens to overlap with summer vacation, and the availability for that week is set to 30%.
Therefore, 3 to 4 points' worth of work, which is 30% of 12 points, becomes the tasks this team should do in the next sprint.

For the week of 8/16, availability is 100%, so if things go as they are, 12 points' worth of work will be put forward in the sprint plan.

## Increment

The increment is the combination of the results of previous sprints and the product backlog items completed in the current sprint.
In most cases it is provided as working software, and at the end of the sprint it must be complete and **working correctly.**
For this reason, the product owner and the development team need to share a common standard for what "done" means.
This is called the **"Definition of Done."**

The Definition of Done varies from team to team. It may be that the code has passed code review,
that all unit tests and E2E tests pass, that deployment is complete,
performance, or a combination of various conditions.

---

So far, we have explained what the Scrum team builds.
Next, let's explain the flow of development in a Scrum team.

{{% button relref="/docs/scrum/events" %}}Events in Scrum{{% /button %}}
