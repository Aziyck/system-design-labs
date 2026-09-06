# Lab 1: Define the Initial Product

## Goal

Define a small Personal Investment Dashboard without designing its internal
architecture or selecting technology.

Before you start, read the
[Lecture 2 home-reading guide](../../lectures/lecture-02-product-scope-and-system-boundaries.md).

The client request is:

```text
Help me follow my investments.
```

This request is too vague. Use evidence and the Lecture 2 method to turn it
into one consistent product contract:

```text
research evidence
  -> stakeholders and actors
  -> product promise
  -> goals and non-goals
  -> user stories and definitions of done
  -> system boundary
  -> C4 System Context view
```



## What to submit

Submit one document with these sections:

1. Product research.
2. Stakeholders and actors.
3. Product promise and scope.
4. Functional requirements.
5. C4 System Context view.



## 1. Product research

Spend 20-30 minutes examining two existing products:

- one market data product, such as Google Finance, Yahoo Finance, or Apple
Stocks;
- one trading product, such as Interactive Brokers or TradingView.

Public pages, product descriptions, screenshots, and app listings are enough.
You do not need an account or paid access.

Start with this research question:

```text
How do existing products help a User follow market information, and which
parts belong in this Dashboard's first version?
```

Complete this table:


| Product | Likely User and goal | Reusable pattern |
| ------- | -------------------- | ---------------- |
|         |                      |                  |
|         |                      |                  |




## 2. Stakeholders and actors

Use the
[UK Government stakeholder-mapping guide](https://analysisfunction.civilservice.gov.uk/policy-store/stakeholder-mapping/)
as a simple method:

1. List people or organizations that are affected by or can influence the
  Dashboard.
2. Mark their motivation as high or low.
3. Mark their influence as high or low.
4. Give one reason for each placement.

Complete this table:


| Stakeholder | Motivation | Influence | Reason |
| ----------- | ---------- | --------- | ------ |
|             |            |           |        |
|             |            |           |        |
|             |            |           |        |


Then place the stakeholders in this matrix:


| Motivation | Low influence | High influence |
| ---------- | ------------- | -------------- |
| High       |               |                |
| Low        |               |                |


Finally, classify each candidate as a direct human actor, an external system,
or another stakeholder. Only directly connected actors and systems belong in
the System Context view.

Use this distinction:

- A stakeholder is affected by the product or can change a product decision.
- An actor is a person or external system that directly interacts with the  
Dashboard.



## 3. Product promise and scope

Write one product promise:

```text
<Product> helps <main User> solve <problem> so that <useful result>.
```

Write:

- five goals;
- three non-goals.

Each goal must state a User-visible result. Each non-goal must remove work from
the first version. Do not use a screen, component, or technology as a goal or
non-goal.

## 4. Functional requirements

Write at least five user stories. Use `DASH-1` as IDs.

Follow the
[Agile Alliance user-story guide](https://agilealliance.org/glossary/user-stories/)
and its [Three Cs](https://agilealliance.org/glossary/three-cs/): write a
short story, discuss its important cases, and confirm them with definitions
of done.

For each story, use three steps:

1. Actor goal: state what the actor needs to achieve or learn.
2. User story: state the actor, goal, and useful result.
3. Definitions of done: list two to four observable checks.

Use this user-story form:

```text
As a <actor>, I want <goal>, so that <useful result>.
```

For each story, write:

```text
Definitions of done:
- <observable normal result>
- <important alternative result>
- <access or scope limit when relevant>
```

Together, the five stories must cover the most important flows.

At least two stories must have a definition-of-done check for a missing,
stale, unsupported, or unauthorized result.

Do not include screens, components, databases, APIs, caches, queues, services,
cloud products, programming languages, or frameworks.

## 5. C4 System Context

Then draw one C4 System Context view. Follow the official
[C4 System Context guide](https://c4model.com/diagrams/system-context):

1. Put the Personal Investment Dashboard in the center.
2. Add each direct human actor.
3. Add each directly connected external system.
4. Label each relationship with its product purpose.

Do not show applications, APIs, databases, caches, queues, services, or cloud
products inside the Dashboard.

For each external system, check:

- which Dashboard responsibility needs it;
- which result it supplies;
- what the User sees when the result is missing, stale, or unsupported.

The external system owns its source result. The Dashboard still owns how that
result becomes clear and safe for the User.

## Checklist

- [ ] I researched at least two existing products.
- [ ] I cited evidence for each selected product pattern.
- [ ] My research changed or confirmed at least one scope decision.
- [ ] I mapped stakeholder motivation and influence.
- [ ] I separated stakeholders, direct human actors, and external systems.
- [ ] My product promise is one clear sentence.
- [ ] My goals and non-goals agree with my product promise.
- [ ] I wrote at least five user stories.
- [ ] Every story has two to four definitions of done.
- [ ] At least two stories include an important alternative result.

- [ ] My external dependencies include a User-visible missing, stale, or unsupported result.

- [ ] I created a C4 System Context view of the Dashboard.
