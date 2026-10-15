---
title: "Manifesto for Agile Software Development"
weight: 1
bookToc: true
---

# Manifesto for Agile Software Development

If we were to give a concise answer to the question of what agile development is, it would be:
**"Development that is grounded in the mindset of the Manifesto for Agile Software Development and follows the 12 Principles of Agile Development."**
Let's start by reading through the [Manifesto for Agile Software Development](https://agilemanifesto.org/iso/en/manifesto.html).

```
Manifesto for Agile Software Development

We are uncovering better ways of developing
software by doing it and helping others do it.
Through this work we have come to value:

Individuals and interactions over processes and tools
Working software over comprehensive documentation
Customer collaboration over contract negotiation
Responding to change over following a plan

That is, while there is value in the items on
the right, we value the items on the left more.
```

This manifesto was created in 2001 by well-known software developers such as Kent Beck and Robert C. Martin (Uncle Bob).
**"Individuals and interactions over processes and tools," "Working software over comprehensive documentation," "Customer collaboration over contract negotiation," and "Responding to change over following a plan"**:
these four are declared as the mindset that must be upheld in agile development.

First, let's break each of these down and understand them.

## Individuals and interactions over processes and tools

What this means is: **"communication between individuals who trust each other is best,"
"following the process is not always right,"
and "avoid being 'used' by your tools."**

Agile development places great importance on close communication between members.
For example, it is very common for projects to use email or mailing lists
as their communication tools.
Some teams may also use something like request forms to communicate between teams.
However, exchanges through such forms and tools take a great deal of time.
What this value is saying is that there are a great many situations where talking directly solves problems faster.

In addition, in agile development the process is constantly reviewed.
One important keyword in agile development is responding quickly to change.
This is not limited to the product specifications; the team also autonomously keeps changing its own development process as needed.
In agile development, the development process that suits a team differs from team to team.
Of course, there are basic ways of thinking such as XP and Scrum, but there is no telling whether they really suit a particular team.
As product development proceeds, each team's development process is optimized and changes.

## Working software over comprehensive documentation

What end users ultimately want
is the product itself.
No matter how thoroughly you write design documents for building the product,
they bring no benefit to users, so that is not where you should focus your effort.
What we should focus on is nothing other than the product itself.

Furthermore, design documents cannot simply be written and thrown away; they must be maintained even after they are written.
In agile development, however, the product specifications keep changing.
Keeping the design documents maintained in line with those changes is very hard.
It is easy to imagine how difficult it is to both maintain the design documents and release the product quickly at the same time.

So we have seen that product design documents are unnecessary. What about comments in programs, then?

The agile way of thinking is that these are also unnecessary.
What this means is that you should not write programs that cannot be understood on a single read in the first place.
Agile developers must write readable, clean code and make the effort to keep it that way.

So, are documents completely unnecessary in agile development?

The answer is NO. For example, the very document you are reading right now
is necessary for developing agile talent and scaling teams.
Beyond that, it is a good idea to have onboarding materials for people to read when they are newly assigned to a project.

The documents needed differ from project to project, but what matters is to **create only the documents you need, when you need them**.

## Customer collaboration over contract negotiation

In the traditional SI business, there has been a customer-vendor relationship,
and vendors have been required, under fixed-price contracts, to build systems that satisfy the functions defined during requirements definition.
However, it is common for issues that are hard to deal with to come up as development proceeds,
or for moments to arise where you think the product would be better if it were done differently.

Yet in the traditional SI business, the contract is absolute.
Doing anything that deviates from the requirements definition partway through is a breach of contract,
and even trying to do it properly means making an additional contract and revisiting the deliverables of earlier phases, which takes an enormous amount of time, effort, and cost.
Nevertheless, both the customer and the vendor should share the desire to deliver a good product to end users.
The contract becomes a major barrier to that goal.
For this reason, in agile development, **carrying out the work under a time-and-materials (quasi-mandate) contract rather than a fixed-price contract** is a fundamental premise.

In agile development, under the goal of delivering better value to end users,
the customer and the vendor work together as one. There must be no hierarchical relationship between customer and vendor.
To achieve that goal, agile places great importance on both the customer and the vendor freely putting forward suggestions such as "Wouldn't it be even better if we did it this way?",
and on quickly realizing them and delivering them to end users.

## Responding to change over following a plan

The waterfall model was a model in which the functions defined during requirements definition
were broken down and estimated, a plan was made, and development proceeded according to that plan.
In many cases, however, things do not go according to plan. Why is that?

Simply put, it is because it is impossible to plan correctly.
Sometimes the functions cannot be correctly captured as requirements during the requirements definition phase,
and sometimes something you thought you had captured as a requirement turns out not to be needed after all.
Also, you may not be able to estimate correctly when estimating the schedule,
or you may run into unexpected issues during implementation.

In waterfall, plans often stop going according to plan,
but agile development takes the approach of moving forward on the assumption that there will be changes.
Because change is assumed, we do not make detailed estimates,
nor do we identify all the requirements in advance.
We put in order the functions that "will definitely be needed," and develop the minimum necessary functions
by adding them a little at a time.
As development proceeds, new functions may turn out to be needed.
Agile development accepts that too.
Agile is a development process premised on change.

---

You can see that the Manifesto for Agile Software Development describes the mindset that developers taking on agile development should have.
Next, let's look at the 12 Principles of Agile Development that form the background to this mindset.

{{% button relref="/docs/agile/principle" %}}12 Principles of Agile Development{{% /button %}}
