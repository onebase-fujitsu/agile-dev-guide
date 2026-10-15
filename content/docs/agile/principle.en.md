---
title: "12 Principles of Agile Development"
weight: 2
bookToc: true
---

# 12 Principles of Agile Development

What was declared in the [Manifesto for Agile Software Development]({{% ref "/docs/agile/manifest" %}})
described the mindset that matters in agile development.
Now let's look at the [12 Principles of Agile Development](https://agilemanifesto.org/iso/en/principles.html) that form its background. There is a link to them on the Manifesto for Agile Software Development page mentioned earlier.

```
Principles behind the Agile Manifesto


We follow these principles:
Our highest priority is to satisfy the customer
through early and continuous delivery
of valuable software.

Welcome changing requirements, even late in
development. Agile processes harness change for
the customer's competitive advantage.

Deliver working software frequently, from a
couple of weeks to a couple of months, with a
preference to the shorter timescale.

Business people and developers must work
together daily throughout the project.

Build projects around motivated individuals.
Give them the environment and support they need,
and trust them to get the job done.

The most efficient and effective method of
conveying information to and within a development
team is face-to-face conversation.

Working software is the primary measure of progress.

Agile processes promote sustainable development.
The sponsors, developers, and users should be able
to maintain a constant pace indefinitely.

Continuous attention to technical excellence
and good design enhances agility.

Simplicity--the art of maximizing the amount
of work not done--is essential.

The best architectures, requirements, and designs
emerge from self-organizing teams.

At regular intervals, the team reflects on how
to become more effective, then tunes and adjusts
its behavior accordingly.
```

Next, let's interpret these principles.

## Our highest priority is to satisfy the customer through early and continuous delivery of valuable software

The most important activity for developers is to **make the customer's business succeed and satisfy the customer**.
To do so, they need to **deliver valuable software quickly and continuously**.

In the waterfall model, the goal of development was to achieve QCD (quality, cost, and delivery).
This is because there is a fixed-price contract between the customer and the vendor,
and the vendor must deliver the product by the agreed deadline, within budget, and with assured quality.
In agile development, however, as mentioned earlier, a time-and-materials (quasi-mandate) contract is the premise.
The customer and the vendor jointly aim to deliver valuable software to end users.
Therefore, **in agile development, the goal of development is not to achieve QCD.**
Of course, having no bugs, lowering costs, and meeting deadlines are important.
However, what we should aim for above all is to deliver valuable software to end users quickly and continuously.
We must not lose sight of that goal.

## Welcome changing requirements, even late in development

Developers actively accept changes **if they are valuable**.
"If they are valuable" is the important point.
We do not accept changes simply because the customer tells us to.

So how do we determine whether something is valuable?

**End users determine whether something is valuable.**
You might wonder, "How, when we haven't even developed it yet?"
Agile development has a process called hypothesis testing for verifying exactly that.
We build a prototype and actually interview users.
Only once we know that building a feature will benefit end users do we start developing it.

In other words, a change in requirements, when it leads to improvement,
is nothing less than proof that we have found new value.
It is important to see changes in requirements as opportunities to discover "new value" and to accept them positively.

## Deliver working software frequently, from a couple of weeks to a couple of months, with a preference to the shorter timescale

In waterfall, products were released by building all the functions identified during requirements definition at once,
testing them all at once once they were all in place, and releasing when that was complete.
With that approach, however, end users can only check the product once it is entirely finished.

That is why, in agile development, we release working software at short intervals and build the product by adding features little by little.
The reason is that you cannot draw out concrete opinions from end users until they actually try the product.
By delivering an actually working product to end users bit by bit,
we make hypothesis testing easier. This in turn leads to delivering better value to end users.

## Business people and developers must work together daily throughout the project

To repeat, in agile development the customer and the vendor
are teammates acting together toward the goal of delivering better value to end users.

In agile development, the situation changes from moment to moment. If you proceed without a visible shared goal under such conditions, misunderstandings and the like may arise.
To reduce such communication errors, it is ideal for **all stakeholders to work in the same place**.

## Build projects around motivated individuals. Give them the environment and support they need, and trust them to get the job done

Gather **motivated, positive people** for the development team.
Many people mistakenly believe that agile development requires development skills, and that you must spend money to gather people with advanced development skills.
In fact, this is half right and half wrong:
the twist is that people with advanced development skills are motivated and positive about learning technology in the first place.
However, that does not mean you necessarily need to gather such people when launching an agile development team.
The right approach is to **gather people who are positive and eager to learn, even if they lack the skills right now**.
Of course, if you can gather highly skilled developers, so much the better, but in reality that is often difficult.
In the first place, in agile development you do not know what the specifications will be until the project actually starts.
In that situation, even if you say "advanced skills," you will likely end up asking, "Skilled in which technologies?"
After all, the application architecture and frameworks cannot be decided until the project actually starts and hypotheses have been tested.
So, as for the members you gather, being good at object-oriented programming is probably enough.

Once you have gathered the people, stakeholders should trust the development team, leave the work to them, and take care that they can work comfortably.
Developers, for their part, must think for themselves and take responsibility for their own actions and decisions in order to create value for end users.

## The most efficient and effective method of conveying information is face-to-face conversation

There are many communication tools in the world, but
**there is no better way to communicate than talking with the other person then and there**.
Direct conversation is the fastest, most efficient, and at the same time most effective.

What matters in communication is that it is two-way.
Email in particular tends to become one-way, such as merely conveying decided specifications.
To build two-way communication,
chat-based communication tools such as Slack and Teams are better.
However, even when using such chat-based communication,
we should communicate with the awareness that nothing beats direct conversation.

In communication, nonverbal cues such as facial expressions and tone of voice convey a great deal of information.
This is because communication through tools cannot make use of those nonverbal elements.

When all stakeholders gather in the same space and talk face to face in both directions, it becomes easier to grasp each other's intentions.

## Working software is the primary measure of progress

In agile development we release working software at short intervals,
and the already completed features that are working become the key measure of progress.

Progress measured by anything other than working software, such as how complete the design documents are, cannot be used as a measure, because problems may go unnoticed until the software actually runs.
That is why, in agile, we plan to release in small units so that we can show working software as early as possible.

If a feature would take several weeks to release, agile judges that feature to be too big.
Features should be broken down until each one can be released in a few hours, or at most two or three days.

**The larger the unit of release, the more there is to consider during development, and the harder it is to estimate.**
Acceptance will also become harder.
Breaking features down into units that take a few hours also has the benefit of making misunderstandings between members less likely.

## Agile processes promote sustainable development. Maintain a constant pace indefinitely

What matters in agile is not overdoing it.
In waterfall development, to achieve QCD, people sometimes work overtime to meet deadlines (this tends to happen especially in the downstream phases).
This is possible because the project has an end. (Although it should not be done,) it is precisely because there is a deadline that people can push themselves too hard.

In agile, however, there is no clear end to the project. Even after the product is released,
development continues in order to deliver better value to end users or to realize the vision the customer is aiming for.
In that context, it is very important not to overdo it and to keep a sustainable, reasonable development pace.
**When people are overworked, they are less inclined to be creative or to make improvements, and overall performance declines.**
Let's keep not only the developers but also the customer in their best condition, both mentally and physically.

## A commitment to leveling up technology and design enhances agility

Developers are expected to **always learn the latest technologies and apply effective technologies to the product**.
In old-fashioned projects in particular, you often hear the phrase "proven in operation."
It refers to the practice of not touching a given part of the code because it has been running in production and has been confirmed to be free of bugs.
However, as features are added, modifying the source code while taking such "proven in operation" parts into account will eventually become unworkable.
Before you know it, the source code has become distorted.

The reason people relied on "proven in operation" until now is that the testing and deployment processes were not automated,
so testing and quality assurance took an enormous amount of time, and relying on it was a way to cut that time.
Conversely, if the testing and deployment processes are automated and can be carried out in a short time,
you should be able to make changes easily without relying on "proven in operation," and keep the software in "the shape it should be."

Keeping software clean is very important for increasing agility.
With distorted software, just determining the scope of impact is a big deal, and even a small change costs a great deal.
Even if there are no problems right after the product is released, the cost of changes will gradually keep growing.
If you can keep the software clean, changes become easy, and even as the overall system grows,
it should remain possible to keep releasing features continuously in short cycles.

However, automating tests and deployments is very hard. It often happens that implementing unit tests is more difficult than implementing the feature itself.
Designing software so that its automated tests complete in a short time is also very hard.
It is very hard, but **automated testing and automated deployment are the watershed for maintaining agility**, and you must not neglect them.

**It is very important that everyone involved in development recognizes the value of learning the latest technologies.**

## Simplicity (the art of maximizing the amount of work not done) is essential

It is often said that agile can develop faster with fewer people than waterfall.
This is half right and half wrong.
The reason agile can develop quickly is that **it does not build anything wasteful (= anything that does not create value)**.
Even if you adopt the practices of Extreme Programming,
if you define all the functions up front as in the waterfall model
and then build all of them, it will take a very long time.
And since, unlike waterfall, there are fewer people, it is as clear as day that it would take even longer than waterfall.

In agile, it is very important to **look for "waste = things that do not create value" and have "the courage to stop."**
This applies not only to the product's features but also to the agile development team's development process.

Everyone finding and reducing waste in their daily work leads to greater agility and higher productivity for the development team.

## Members of a self-organizing team achieve higher performance by working together

The term "self-organizing team" has come up. This is exactly what an agile development team should aim to be.
**A self-organizing team is a team that, rather than being directed from outside the team, has the autonomy to choose the best way to accomplish its own work.**
The development team decides its own discipline and rules, and takes the initiative in improving its work and solving problems.

A self-organizing team is easy to talk about but very hard to achieve.
Obviously, a team does not suddenly become self-organizing one day.
At the very least, it cannot happen without members who want to change things,
and a self-organizing team is something you finally achieve only when such members steadily encourage change, little by little.

What happens when you become a self-organizing organization is that the team no longer depends too heavily on specific members.
As a result, the project is less likely to stall even if a particular person leaves.
It also has the effect of increasing members' sense of ownership.
That, in turn, leads to improved performance.

## Improve the performance of the development team through regular retrospectives

This is related to the self-organizing team discussed above:
in agile, so that the team keeps growing, all development members hold a retrospective (often called a "retro") every week.
This retrospective is the most crucial part of the agile development process.

In the retro, the team uncovers the problems it is facing and the waste in its work.
For example, performance did not improve this week, or remote work is difficult:
this is where the team shares the frustrations that come up in day-to-day development. The frustrations members have are precisely the points for improvement.
What matters here is that **the various problems faced in development are problems the team should tackle together, not something to pin on individuals**.

For example, suppose a defect was introduced during development. Or suppose inefficient automated tests were written.
An agile team considers the development process that allowed the problem to be introduced to be the problem itself, and the whole team thinks about how to deal with it.
The individual who introduced the problem must never be blamed.

The fact that no particular individual gets blamed means that psychological safety is high.
**Unless the team's psychological safety is high, members cannot even share their frustrations.**
It is important to create an atmosphere in the retrospective where people can casually raise anything.

---

This concludes the overview of agile. Finally, let's sum up.

{{% button relref="/docs/agile/conclude" %}}Summary{{% /button %}}
