# Lab 2: Quantify the Dashboard Reads

## Goal

Turn the reviewed Lab 1 behavior into measurable quality targets, a read
workload estimate for three scales, and a market-data storage estimate.

Before you start, read the
[Lecture 3 home-reading guide](../../lectures/lecture-03-quality-requirements-workload-and-capacity.md).

## Starting point from the Lab 1 review

- The authenticated User is the direct human actor.
- The Market Data Provider is the external system.
- The first version supports market overview, filtering, search, Stock detail,
price history, and a private Watchlist.
- Market prices show provider time or delay.
- A missing price is unavailable, not zero.
- A User cannot read or change another User's Watchlist.

The reviewed read set for this lab is:

- Overview;
- Filter;
- Stock price;
- History;
- Watchlist;
- Search.

## Client brief

The client gives these expectations:

- Stock prices should feel immediate.
- Most correct reads should complete within 2 seconds during busy periods.
- Expected provider delay is about 15 minutes.
- Older data must be visibly delayed or unavailable.
- Dashboard uptime must be measurable separately during market hours and the
rest of the day.

Treat these statements as product inputs. When a target is missing, select one
and explain the assumption. A quality requirement must contain:

```text
measure + target + operating condition
```

Use the same User behavior for each scale:


| Read        | User behavior                            |
| ----------- | ---------------------------------------- |
| Overview    | 70% refresh every 30 seconds             |
| Filter      | 50% change the filter 3 times per minute |
| Stock price | 20% request once per second              |
| History     | 20% request once per 5 minutes           |
| Watchlist   | 60% refresh once per minute              |
| Search      | 10% search 3 times per minute            |


Use these concurrent-User buckets:

- 300;
- 3,000;
- 30,000.

At market open:

- 30% refresh the overview once during 10 seconds;
- 60% of that group also refresh the Watchlist;
- add these actions to steady traffic;
- add a 10% capacity margin after calculating the subtotal;
- round up only the final capacity target.

Assume that each listed User action creates one Dashboard request. Do not
assume that one Dashboard request creates one provider request. Provider
traffic needs a separate model and evidence.

## What to submit

1. Quality requirements.
2. Steady RPS estimates.
3. Market-open RPS estimates.
4. Storage estimates.
5. Potential bottlenecks.

## 1. Quality requirements

Write measurable requirements for the four core qualities:

### Read latency

- Define the start and end of the measurement.
- Use a percentile for "most" reads.
- State what happens at the 2-second limit.

### Uptime-style availability

- Define when the Dashboard is usable.
- Define which hours belong to the market-hours window.
- Choose an uptime target in nines for market hours.
- Choose an uptime target in nines for the rest of the day.
- Justify why the two windows need the same or different targets.
- Measure the two windows separately over 30 days and calculate the downtime  
budget for each one.

### Consistency

- For a Stock price, state the maximum accepted staleness and when the result
becomes unavailable.
- For a Watchlist change, state what the same User's next Watchlist read must
show after the change completes.

Each requirement must name its measure, target, and operating condition. Keep
an unavailable result separate from a correct data result. A fast unavailable
result can meet the latency target and still fail another quality requirement.

## 2. Steady RPS estimates

Use this formula for every row and every scale:

```text
RPS = concurrent Users x participating share x actions per User / seconds
```

Complete this table. Show the calculation, units, and unrounded result before
each total.


| Read             | 300 Users | 3,000 Users | 30,000 Users |
| ---------------- | --------- | ----------- | ------------ |
| Overview         |           |             |              |
| Filter           |           |             |              |
| Stock price      |           |             |              |
| History          |           |             |              |
| Watchlist        |           |             |              |
| Search           |           |             |              |
| **Steady total** |           |             |              |




## 3. Market-open RPS estimates

Use the market-open behavior in the client brief. Estimate the continuing
steady traffic and the two additional flows shown below. Do not count the
steady Overview or Watchlist traffic twice. Complete all market-open
calculations in one table:


| Market-open calculation           | 300 Users | 3,000 Users | 30,000 Users |
| --------------------------------- | --------- | ----------- | ------------ |
| Steady read traffic               |           |             |              |
| Additional Overview refresh flow  |           |             |              |
| Additional Watchlist refresh flow |           |             |              |
| **Market-open subtotal**          |           |             |              |
| 10% capacity margin               |           |             |              |
| **Rounded-up market-open target** |           |             |              |


Round up only the final market-open target.

## 4. Storage estimates

The Dashboard must synchronize the market data that it needs from the Market
Data Provider into its own system. Estimate the raw data that the Dashboard
must store. 

### Research and define a Stock

Research what a "Stock" can mean in a market-data product. Then define what it
means for this Dashboard.

Make and justify these product decisions:

- which countries, markets, or exchanges the first version supports;
- which instrument types are included or excluded, such as common stock,
preferred stock, ADRs, ETFs, or funds;
- whether inactive or delisted instruments remain available;
- how many Stocks the selected scope contains.

Cite at least one source for the instrument definition and one source for the
estimated number of supported Stocks. State the date when each number was
observed.

### Define the synchronized data

List the data required for the Lab 1 flows. Decide what the Dashboard stores
for:

- Stock identity, search, and filter information;
- the latest price and its provider time;
- price history, including interval and retention;
- any other provider data required by the selected product scope.

Connect each stored field or data set to a product need. Do not copy every
provider field without a reason. State the synchronization frequency as an
assumption, but do not estimate provider request traffic in this section.

### Calculate the storage

Create a representative stored record or data sample and use it to estimate
the average bytes per record. Complete this table:

```text
raw storage = record count x average bytes per record

history record count
  = supported Stocks x history points per Stock per day x retained days
```


| Data set             | Product decision and retention | Record-count calculation | Bytes per record | Raw storage |
| -------------------- | ------------------------------ | ------------------------ | ---------------- | ----------- |
| Stock reference data |                                |                          |                  |             |
| Latest prices        |                                |                          |                  |             |
| Price history        |                                |                          |                  |             |
| Other selected data  |                                |                          |                  |             |
| **Total**            |                                |                          |                  |             |




## 5. Find and analyze the potential bottlenecks

Use your quality requirements, RPS estimates, and System Context view. Find at
least one potential bottleneck for each quality. A high RPS value is a reason
to investigate a path. It is not proof that the path is a bottleneck.


| Quality      | Potential bottleneck | Evidence from this lab | Possible effect | What to measure next |
| ------------ | -------------------- | ---------------------- | --------------- | -------------------- |
| Latency      |                      |                        |                 |                      |
| Consistency  |                      |                        |                 |                      |
| Throughput   |                      |                        |                 |                      |
| Availability |                      |                        |                 |                      |


For each row, explain:

1. which read path, state path, or external dependency creates the pressure;
2. how it can cause the quality target to fail;
3. which evidence supports the hypothesis;
4. which measurement would confirm or reject it.

## Checklist

- [ ] I wrote measurable requirements for all core qualities.
- [ ] I chose and justified uptime targets for both parts of the day.
- [ ] I showed the steady and market-open RPS calculations for all three scales.
- [ ] I researched and defined the supported Stock scope.
- [ ] I estimated initial, daily, and one-year raw market-data storage.
- [ ] I stated assumptions, units, windows, and final rounding.
- [ ] I analyzed one potential bottleneck for each core quality.