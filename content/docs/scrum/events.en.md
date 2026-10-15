---
title: "Events"
weight: 4
bookToc: true
---

# Events

![The Scrum process](../process.jpg)
Image source: [アジャイル開発とは（中編）(What is Agile Development? Part 2)](https://www.fujitsu.com/jp/group/fst/about/resources/featurestories/about-agile-02.html)

Fujitsu's public website happens to have a diagram explaining the Scrum process, so I will use it to explain.

## Sprint

In Scrum, development is carried out repeatedly in fixed periods ranging from one week up to a maximum of one month. This fixed period is called a **sprint** (sometimes also called an iteration).
Within each period, the development team does all the work needed to complete product backlog items, such as planning, design, development, and testing.

By repeating development in fixed periods like this, the development team develops a rhythm and can concentrate,
and it becomes easier to see progress toward the overall goal and to deal with risks.

## Sprint planning

The very first thing done at the start of a sprint is sprint planning.
It is also sometimes called iteration planning.

It is a meeting where the product owner and the entire development team gather to decide what to do in the current sprint.
Roughly speaking, three things are done in this meeting:

* Reviewing the content of the product backlog
* Estimating the backlog work items
* Deciding the sprint goal

### Preparation

Before sprint planning, as preparation, the product owner
takes the items they want to do in the next sprint from the product backlog,
clarifies the work involved, and breaks it down into units of work. Each task is broken down so that it can be finished in a few hours to under a day.

This work is called **product backlog refinement**,
and is sometimes simply called refinement.

### Planning meeting

When the sprint planning meeting starts, the product owner first reads out the content of the refined backlog
and shares it with the development members.

Once the work has been shared with the development members, the developers estimate the amount of work for each task on the spot.
A method such as **planning poker** is often used for estimation.

Picture all the developers simultaneously putting forward their estimates of the amount of work a task will take, right then and there.
It may help to imagine something like a game of rock-paper-scissors.
The important point is that everyone gives their estimate at the same time;
this way, experts and non-experts alike give their estimates simultaneously.

In meetings like this, people around often get pulled along by the experts' opinions,
so it is important to make sure everyone can contribute their opinions equally.

If the developers' estimates differ, they discuss it on the spot and reach agreement.
Discussion is the important point: **unexpected implementation issues and risks can be discovered during discussion**.
Conversely, you may realize that something can actually be implemented easily.
Here too, it is important to take care to draw out everyone's opinions through appropriate facilitation,
such as letting others speak before the experts.

Once the work items have been estimated, the team compares them against its velocity
and decides how much to implement in this sprint. This is called the **sprint goal**.

## Daily scrum

The daily scrum is a short meeting (about 10 to 15 minutes) held every day during the sprint by the product owner and the development members.
They check the remaining work in the sprint backlog and confirm whether the sprint goal can be achieved if they continue as they are.
It is often held every morning as a morning meeting, but Scrum does not specify when it should be held. However, it is important to hold it at the same time every day.

There are no particular rules for how to run the daily scrum, but a common format is for everyone to go over the following:

* What I worked on yesterday
* What I plan to work on today
* **Anything I am concerned about**

The third point, "anything I am concerned about," is especially important.
If there is anything likely to get in the way of achieving the sprint goal, this is where it gets shared.
However, **the daily scrum is not a place for solving problems**, so keep it to sharing issues here,
and if separate work items are needed to solve a problem, deal with it by, for example, adding them to the sprint backlog.

## Sprint review

The sprint review is where the results of the sprint are reviewed.
The product owner actually tries out the product the development team has built,
and makes clear what was completed and what was not completed in the sprint backlog.

The main purpose of the sprint review is to **get feedback**.
For this reason, stakeholders also take part in the sprint review to give feedback on the product.
It is also a place to align with stakeholders on the state of the product and the business situation,
and to reflect this in the product backlog as needed.

## Sprint retrospective

When a sprint ends, a retrospective called the sprint retrospective is held at the end of the sprint.

In the sprint retrospective, all development team members discuss
what problems there were in that sprint's product development and whether there is anything they can do to achieve better results.

What matters here is that **individuals must not be blamed for the problems and issues raised here**.
This is as described in the [12 Principles of Agile Development]({{% ref "/docs/agile/principle#improve-the-performance-of-the-development-team-through-regular-retrospectives" %}}).
Even if someone introduced a bug, no particular individual must be blamed;
instead, all members discuss what to do about the process that allowed the bug to be introduced.

**Discuss not only what to improve in this sprint, but also what went well.**
Among the things that went well, there may be habits worth continuing.

The whole team discusses what to improve and what went well during the sprint, and what to do about waste in the development process going forward,
and summarizes the results as action items.

These are reflected in the next sprint backlog to make the team better.

An important keyword in agile is the self-organizing team.
This is exactly the goal that an agile development team aims for,
and **the process for forming that self-organizing team is this sprint retrospective**.
The author considers it the most important process among the Scrum events.

---

So far, we have explained Scrum's roles, artifacts, and events.
You should now have a rough idea of how development proceeds.
Finally, let's summarize Scrum.

{{% button relref="/docs/scrum/conclude" %}}Summary{{% /button %}}
