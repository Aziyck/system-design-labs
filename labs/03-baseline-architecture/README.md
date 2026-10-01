# Lab 3: Draw the Dashboard Boundary

## Goal

Create one Mermaid System Context diagram, one Container diagram, and one
Component diagram for the Dashboard. Then test the design with three sequence
diagrams. Put all six diagrams in fenced `mermaid` blocks and keep the source
in your submission.

Read [Lecture 4](../../lectures/lecture-04-architecture-decomposition-and-data-flows.md),
including the Market Data Collector walkthrough.

## Given information

An authenticated User can browse and filter Stocks, read prices and history,
and manage a private Watchlist. A Market Data Provider supplies delayed data
and can return invalid data, rate limits, or outages.

Use the load targets from Lab 2.

## 1. System-context diagram

Draw the system boundary, external actors and systems, and labeled
relationships. Do not show internal runtime parts.

## 2. Container diagram

Open the system boundary. Choose the smallest set of runtime parts and stores
that can meet the requirements. Give each part one responsibility and label
every relationship.

## 3. Component diagram

Open the Dashboard Application from your Container diagram. Show its main
components, their responsibilities, and the containers or external elements
that communicate directly with them. Do not show classes, functions, or
separately deployed applications as components.

## 4. Sequence diagrams

Draw these three flows. Use participants from your architecture views and
keep their names consistent. Show requests, results, and alternative outcomes
with `alt` blocks. State any starting assumptions below each diagram.

### A. Sync market data

Show what starts a sync, how provider data is checked, and when accepted data
becomes available to later reads. Include invalid data and a provider timeout
or rate limit. State what happens to previously accepted data when sync fails.
Keep provider credentials on the server.

### B. Read a Stock price

Show identity and input checks, the data read, and the result shown to the
User. Include a missing price, data older than your accepted limit, and a
storage failure.
Make clear whether a User read needs a new provider request.

### C. Change a private Watchlist

Show identity, input, and permission checks before a change. Include an
unauthorized attempt and a storage failure. Mark the point at which success
can be reported and state what the same User's next read must show.


## Checklist

- [ ] I included one System Context, one Container, and one Component diagram.
- [ ] I included the three required sequence diagrams.
- [ ] All six Mermaid blocks render without an error.
- [ ] The Component diagram opens only the Dashboard Application.
- [ ] My flows show normal results, rejected actions, and failure results.
- [ ] The design covers normal reads, private data, provider ingestion, and failures.
