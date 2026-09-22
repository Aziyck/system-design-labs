# Lab 2: Quantify the Dashboard Reads

> Working order: Sections 2 → 3 → 1 → 4 → 5. The quality requirements in Section 1 need the RPS numbers as their operating condition, so the workload is calculated first.

## Starting point (from the Lab 1 review)

This lab uses the reviewed Lab 1 scope, which is narrower than the original Lab 1 submission:

- The authenticated User is the direct human actor.
- The Market Data Provider is the external system.
- V1 supports market overview, filtering, search, Stock detail, price history, and a private Watchlist.
- Market prices show provider time or delay.
- A missing price is unavailable, not zero.
- A User cannot read or change another User's Watchlist.

Reviewed read set: **Overview, Filter, Stock price, History, Watchlist, Search.**

Differences from the original Lab 1 worth noting: the news feed and the news provider drop out entirely; the User is now authenticated; named lists become one private Watchlist; and multi-stock comparison is not part of the reviewed read set.

Shared assumption for all sections: each listed User action creates one Dashboard request. One Dashboard request does **not** necessarily create one provider request — provider traffic needs its own model and is out of scope here.

---

## 1. Quality requirements

Design point: the **3,000 concurrent User** bucket. Steady load is 792 RPS; the market-open target is 1,030 RPS (Sections 2 and 3). All targets below are stated against the market-open window, since that is the condition they must survive.

Throughout: an unavailable or error result is **not** an acceptable completed read. A fast unavailable result can meet a latency target and still fail availability or consistency.

### Read latency

**Stock price** — measured client-observed (client send → result usable), because "feels immediate" describes what the person experiences, not what the server clocks.

> During the market-open window, with total Dashboard load up to 1,030 RPS, Stock price read latency must be p50 ≤ 300 ms and p95 ≤ 1 s. A read that has not returned within 2 s is treated as unreachable and shown as unavailable rather than left loading.

**All other reads** — also measured client-observed, for consistency with the above; no sufficient reason was found to switch measurement style mid-product. Note that client-observed includes network and render time, so the User's wait is what is being bounded here.

> During the market-open window, with total Dashboard load up to 1,030 RPS, read latency must meet:

| Read      | p50     | p95   | Cutoff (shown as error/unavailable) | Reasoning                                                                                                                                   |
| --------- | ------- | ----- | ----------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| Filter    | ≤ 0.2 s | ≤ 2 s | 2.5 s                               | Filtering is light work; the 2 s p95 matches the client brief's general bar                                                                 |
| History   | ≤ 1.5 s | ≤ 8 s | 10 s                                | Most reads are short ranges (1D, 1W); long ranges (1Y, 5Y) carry far more points, so the expensive case belongs in the tail, not the median |
| Watchlist | ≤ 1 s   | ≤ 2 s | 5 s                                 | p50 allows for the market-open Watchlist burst                                                                                              |
| Search    | ≤ 1 s   | ≤ 2 s | 5 s                                 | General 2 s bar from the client brief                                                                                                       |
| Overview  | ≤ 1 s   | ≤ 2 s | 5 s                                 | p50 allows for the market-open Overview burst                                                                                               |

Evidence note: an informal check of Yahoo Finance showed a stock graph taking roughly 7–8 seconds to load (observed September 2026). This is not a standard to match — it is evidence that a heavy chart read plausibly lands in the multi-second range, which justifies History carrying a longer tail than the other reads.

### Uptime-style availability

**Definition of usable.** The Dashboard counts as usable when it can return market data inside its staleness rule — not merely when the server responds. The provider's normal delay is about 15 minutes, so that delay is baseline, not a fault. A tolerance of a further 15 minutes is allowed (15 min delay × 2); beyond **30 minutes**, data is served as unavailable and the Dashboard is counted as down for that period. A page that loads but can only show unavailable prices is not usable.

**Measurement windows.** Reference exchange: **Frankfurt Stock Exchange (Xetra)**, core session 09:00–17:30 local (08:00–16:30 UTC) = 8.5 hours per day.

| Window | Hours/day | Measured time over 30 days |
| ------ | --------- | -------------------------- |
| Market hours | 8.5 | 255 h |
| Rest of day | 15.5 | 465 h |

Simplifying assumption: measured over 30 *calendar* days. Xetra does not trade on weekends, so a stricter model would use ~21–22 trading days (≈187 h market hours, ≈533 h rest of day). Calendar days are kept here for exercise simplicity; a production target should use trading days.

**Targets and 30-day downtime budgets**

| Window | Target | Downtime budget |
| ------ | ------ | --------------- |
| Market hours | 99% | 255 × 0.01 = **2.55 h** |
| Rest of day | 97% | 465 × 0.03 = **13.95 h** |

> During each 30-day window, the Dashboard must be usable for at least 99% of measured market-hours time (Xetra core session) and at least 97% of measured non-market time.

**Why the two windows differ.** Uptime weights every second equally, but user impact is concentrated: ten unavailable minutes at midday during peak activity damages far more Users than ten minutes at 03:00. A looser non-market target also buys something concrete — maintenance and deploys can be scheduled there deliberately.

**Why not 99.9%.** V1 is a view-only dashboard with no trading. When it is unavailable, the cost to a User is that they check back later, not a lost or mispriced trade. Under the lecture's ROI framing, the extra engineering, monitoring, and recovery work needed for another nine is not justified by the harm avoided. 2.55 h of monthly market-hours downtime is acceptable at this scope.

**Revisit condition.** This target is tied to current product scope. If the product later adds trading (noted as a future direction in Lab 1), the market-hours target must be re-evaluated — a trading path can lose more value in a few unavailable minutes than a higher target costs.

### Consistency

**Rule A — Stock price freshness.** The Market Data Provider owns the source value; the Dashboard owns how that value is presented.

> Return the last accepted market price together with its provider timestamp and a **delayed** label while its age is within the maximum tolerance of 30 minutes (≈15 min expected provider delay + 15 min tolerance). When no accepted price exists, or its age exceeds 30 minutes, return **unavailable**. Never display zero, and never present an older price as current.

Three distinct results, which must not be collapsed:

| Age of accepted price | Result shown |
| --------------------- | ------------ |
| 0–30 minutes | Price shown, with provider time and a delayed label — this is normal operation, since data is ~15 min behind even when healthy |
| Over 30 minutes | Unavailable — the price is not displayed at all |
| No accepted price ever received | Unavailable — never zero |

Reasoning for refusing rather than labeling beyond the tolerance: in a finance product, Users read the number and skim the label. A 45-minute-old price with a small "stale" tag is a false success, which is more harmful than a clear unavailable result.

**Rule B — Watchlist read-after-write and authorization.**

> Only the authenticated owner may read or change their own Watchlist. After a Watchlist change is confirmed, that same User's next Watchlist read must show the updated contents — the previous contents plus the added Stock, or minus the removed one.

Four results that must remain distinguishable:

| Situation | Result shown |
| --------- | ------------ |
| Owner reads after a confirmed change | Updated Watchlist contents |
| Another User attempts a read or change | Access forbidden — **not** an empty list |
| Watchlist is genuinely empty | A specific empty state |
| Watchlist cannot be fetched | A clear error — **not** an empty list |

A forbidden list, an empty list, and a failed fetch look identical if all three render as "no items." Keeping them separate is the same false-success principle applied to owned state rather than provider data.

---

## 2. Steady RPS estimates

Formula:

```text
RPS = concurrent Users x participating share x actions per User / seconds
```

All windows are converted to seconds before dividing. Rows are left unrounded; rounding happens only at the final capacity target in Section 3.

| Read | Calculation (300 Users) | 300 Users | 3,000 Users | 30,000 Users |
| ---- | ----------------------- | --------- | ----------- | ------------ |
| Overview | `300 x 0.70 x 1 / 30` | 7 | 70 | 700 |
| Filter | `300 x 0.50 x 3 / 60` | 7.5 | 75 | 750 |
| Stock price | `300 x 0.20 x 1 / 1` | 60 | 600 | 6,000 |
| History | `300 x 0.20 x 1 / 300` | 0.2 | 2 | 20 |
| Watchlist | `300 x 0.60 x 1 / 60` | 3 | 30 | 300 |
| Search | `300 x 0.10 x 3 / 60` | 1.5 | 15 | 150 |
| **Steady total** | `7 + 7.5 + 60 + 0.2 + 3 + 1.5` | **79.2** | **792** | **7,920** |

The model is linear in concurrent Users, so the 3,000 and 30,000 columns are the 300-User column scaled by 10 and 100 with every other assumption held constant. This is a property of the formula, not the lecture's 10x sensitivity test, which deliberately stresses one input to see how the model reacts.

**Observation:** Stock price alone accounts for 60 of 79.2 RPS — about 76% of modeled steady traffic, more than all other reads combined. This is a hypothesis to investigate first in Section 5, not proof of a bottleneck.

---

## 3. Market-open RPS estimates

Market-open behavior: 30% refresh the overview once during 10 seconds; 60% of *that group* also refresh the Watchlist (a nested share, not 60% of all Users). These bursts are added on top of steady traffic, which continues unchanged — the steady Overview and Watchlist rows are not replaced or recounted.

| Market-open calculation | Calculation (300 Users) | 300 Users | 3,000 Users | 30,000 Users |
| ----------------------- | ----------------------- | --------- | ----------- | ------------ |
| Steady read traffic | from Section 2 | 79.2 | 792 | 7,920 |
| Additional Overview refresh flow | `300 x 0.30 x 1 / 10` | 9 | 90 | 900 |
| Additional Watchlist refresh flow | `300 x 0.30 x 0.60 x 1 / 10` | 5.4 | 54 | 540 |
| **Market-open subtotal** | `79.2 + 9 + 5.4` | **93.6** | **936** | **9,360** |
| 10% capacity margin | `93.6 x 0.10` | 9.36 | 93.6 | 936 |
| **Rounded-up market-open target** | `93.6 x 1.10 = 102.96` | **103** | **1,030** | **10,296** |

Rounding note: at 300 Users, 102.96 rounds up to 103; at 3,000 Users, 1,029.6 rounds up to 1,030. At 30,000 Users the result is 10,296 exactly, so no rounding is needed. Only this final target is rounded — all intermediate rows keep full precision.

The 10% margin creates space above the estimate. It does not compensate for missing operations, weak behavior assumptions, or a badly chosen peak window.

---

## 4. Storage estimates

### Defining a "Stock" for this Dashboard

| Decision | Choice | Justification |
| -------- | ------ | ------------- |
| Markets / exchanges | Frankfurt Stock Exchange (Xetra) only | Consistent with the market-hours window used for the availability targets in Section 1 |
| Instrument types included | Common stocks (e.g. SAP, Siemens, BMW, Adidas, Allianz) and ETFs (e.g. iShares Core DAX UCITS ETF, iShares Core MSCI World UCITS ETF) | Covers what a v1 personal dashboard User would realistically track |
| Instrument types excluded | Preferred stocks, ADRs, traditional funds, ETCs, ETNs | Overkill for v1; each adds identity and pricing complexity without a v1 product need |
| Delisted / inactive instruments | Not shown in the active instrument list; historical data retained where available | A 5Y History chart should not break when an instrument delists |

**Supported instrument count**

| Category | Count | Observed |
| -------- | ----- | -------- |
| Shares | 1,440 | 22 September 2026 |
| ETFs | 2,666 | 22 September 2026 |
| **Total supported instruments** | **4,106** | |

Source: Deutsche Börse Cash Market, Tradable Instruments — Xetra: https://www.cashmarket.deutsche-boerse.com/cash-en/trading/Tradable-Instruments-Xetra/xetra/

Notes on the count:
- The same page lists 459 "Active ETFs." This refers to actively managed ETFs, a **subset** of the 2,666 ETFs, and is not added to the total.
- The share figure counts tradable share instruments, not companies — one company may have more than one listed share instrument.

**Stated simplification:** retained history for delisted instruments is excluded from the storage estimate below. In reality this creates an accumulating tail of historical data beyond the 4,106 active instruments, which would grow with each year of delistings. Bounding or estimating that tail is out of scope for the v1 estimate but would be needed before any real retention decision.

### Synchronized data

Every stored field traces to a read from the Lab 1 review. Provider fields without a product need are not stored.

| Read | Fields required | Why |
| ---- | --------------- | --- |
| Search | `instrument_id`, `ticker`, `name` | Matching what the User types |
| Filter | `instrument_type`, `sector` | Country/region no longer discriminates, since every instrument is Xetra-listed; type (Share/ETF) and sector are the meaningful axes in this scope |
| Overview | `instrument_id`, `ticker`, `name`, `current_price`, `previous_close`, `price_timestamp` | Gainers/losers need a current price *and* a reference price to compute percentage change; the timestamp is required wherever a price is shown |
| Stock price | `instrument_id`, `current_price`, `price_timestamp`, `previous_close` | The timestamp is mandatory — the consistency rule requires a delayed label on every displayed price |
| History | `instrument_id`, `timestamp`, `price` | The time series behind the 1D–5Y charts |
| Watchlist | `user_id`, `instrument_id` | User-owned state, not provider data — excluded from the market-data sync |

**Synchronization frequency (assumption)**

| Data | Frequency | Reasoning |
| ---- | --------- | --------- |
| Current prices | Every 5 minutes during market hours | Worst-case data age = 15 min provider delay + 5 min since last sync = 20 min, inside the 30-min tolerance from Section 1. A 15-minute sync would give 15 + 15 = 30 min exactly, leaving zero margin — any delayed job would push prices to unavailable |
| Historical prices | Once daily, after the trading session closes | Daily candles are final once the session ends |
| Instrument metadata | Once daily, or on detected change | Ticker, name, type and sector change rarely |

Provider request traffic is not modelled here — one Dashboard request does not imply one provider request.

### History series consolidation

6M, 1Y and 5Y all use a 1-day interval, so they are served by **one** daily series read at different slice lengths, not three separate series. Four stored series total:

| Series | Interval | Retention | Serves | Points/instrument/day | Points/instrument retained |
| ------ | -------- | --------- | ------ | --------------------- | -------------------------- |
| Intraday | 5 min | 2 days | 1D | 510 / 5 = 102 | 102 × 2 = 204 |
| Short | 15 min | 2 weeks | 1W | 510 / 15 = 34 | 34 × 14 = 476 |
| Medium | 4 hours | 2 months | 1M | ≈ 2 | 2 × 60 = 120 |
| Daily | 1 day | 5 years | 6M, 1Y, 5Y | 1 | 1 × 365 × 5 = 1,825 |
| **Total** | | | | **139** | **2,625** |

Assumptions and flags:
- Session length 8.5 h = 510 minutes (Xetra core session).
- The 4-hour interval does not divide 510 minutes evenly; 2 points/day is used.
- Retention uses **calendar** days (2 weeks = 14, 1 month = 30, 1 year = 365), consistent with Section 1. Since no intraday points are generated on weekends, this deliberately **overestimates** — the safe direction for a capacity estimate.

### Storage calculation

```text
raw storage = record count x average bytes per record
history record count = supported instruments x points per instrument
                     = 4,106 x 2,625 = 10,778,250
```

Compact binary field storage is assumed (not JSON with repeated field names, which would cost roughly 3–4× more per row).

| Data set | Product decision and retention | Record-count calculation | Bytes per record | Raw storage |
| -------- | ------------------------------ | ------------------------ | ---------------- | ----------- |
| Stock reference data | All supported instruments, kept while listed | 4,106 | 200 B (`id` 4 + `ticker` ~10 + `name` ~100 + `type` ~15 + `sector` ~50, rounded up) | 821,200 B ≈ 0.78 MiB |
| Latest prices | One per instrument, overwritten each sync | 4,106 | 32 B (`id` 4 + `price` 8 + `timestamp` 8 + `prev_close` 8, rounded up) | 131,392 B ≈ 0.13 MiB |
| Price history | Four series, retention per table above | 10,778,250 | 24 B (`id` 4 + `timestamp` 8 + `price` 8, +4 overhead) | 258,678,000 B ≈ 246.7 MiB |
| Other selected data | None — Watchlist is User state, not synchronized market data | — | — | — |
| **Total** | | | | **259,630,592 B ≈ 247.6 MiB ≈ 0.242 GiB** |

**Initial, daily, and one-year figures**

| Metric | Value | Meaning |
| ------ | ----- | ------- |
| Initial storage | ≈ 247.6 MiB | Assumes a 5-year daily backfill at launch, required to serve the 5Y chart on day one |
| Daily ingest | 139 × 4,106 = 570,734 records/day × 24 B = 13,697,616 B ≈ **13.1 MiB/day** | New history written per trading day |
| One-year ingest | 570,734 × 365 × 24 B = 4,999,629,840 B ≈ **4.66 GiB/year** | Total history *written* over a year |
| One-year stored | still ≈ **247.6 MiB** | Total history *retained* after a year |

The last two rows differ because retention caps every series: intraday, short and medium data expire after 2 days, 2 weeks and 2 months, and the daily series stops growing at its 5-year limit. With a full backfill at launch the system starts at steady state, so old points roll off as new ones arrive and stored volume stays flat. Ingest volume indicates sync and write pressure; stored volume indicates capacity. They answer different questions and should not be conflated.

Excluded from this estimate, per the lecture: indexes, replicas, logs, backups, temporary copies — and the delisted-instrument history tail noted above.

---

## 5. Potential bottlenecks

Each row is a hypothesis to test, not a proven cause. A high RPS value is a reason to investigate a path first; it is not evidence that the path limits performance.

| Quality | Potential bottleneck | Evidence from this lab | Possible effect | What to measure next |
| ------- | -------------------- | ---------------------- | --------------- | -------------------- |
| Latency | The Stock price read path, with the History read as a rival candidate | Stock price is 600 of 792 RPS steady (~76%) and stays at ~600 of 1,030 at market open — highest volume combined with the strictest target (p95 ≤ 1 s). History is far lower volume but each 5Y read pulls ~1,825 points | The price path repeats near-identical work, since many Users request the same popular instruments. Separately, a few concurrent expensive History queries can occupy shared resources and inflate p95 for *every* read, including price | Measure p50/p95 per read path separately under market-open load; measure how much of the price path's work is repeated for the same instruments; measure whether concurrent History reads correlate with p95 rises on other paths |
| Consistency | Price synchronization batches feeding Overview calculations | Prices sync every 5 min against a 30-min maximum age; Overview needs both `current_price` and `previous_close` | A failed or slow sync leaves some instruments with newer prices than others, so Overview may compute gainers/losers from a mixed snapshot — ranking instruments against each other using values from different points in time | Measure sync duration and success/failure rate; track price age per instrument; verify that all `current_price` values used in one Overview calculation come from the same accepted sync snapshot |
| Throughput | The market-open burst | Load steps from 792 to 1,030 RPS inside a 10-second window — a step change, not a ramp | Queueing, CPU saturation, or connection-pool exhaustion can appear at a sudden step even when the same steady load is handled comfortably | Run a load test that steps directly to 1,030 RPS for the market-open window and count reads that **complete within their latency targets** — not responses returned. A server answering 1,030 requests/s where a large share are timeouts has not met the target |
| Availability | The Market Data Provider as a single point of failure | The System Context has exactly one external system, and the usability definition in Section 1 counts the Dashboard as down when price age exceeds 30 minutes | A provider outage longer than ~15 minutes converts directly into measured Dashboard downtime, consuming the 2.55 h monthly market-hours budget quickly. There is no second source to fall back to | Measure provider availability and response latency; sync-job success rate; age of the newest accepted price; how often and for how long price age exceeds 30 minutes during market hours |

Conclusion form, per the lecture: the Stock price path is a potential latency pressure point because it carries the largest share of modelled traffic under the strictest target. The next design must be able to serve repeated reads of the same instrument without repeating all the work. Measured per-path latency and repetition rates are still needed before selecting any mechanism.

---

## Checklist

- [x] Measurable requirements for all core qualities
- [x] Uptime targets chosen and justified for both parts of the day
- [x] Steady and market-open RPS calculations shown for all three scales
- [x] Supported Stock scope researched and defined
- [x] Initial, daily, and one-year raw market-data storage estimated
- [x] Assumptions, units, windows, and final rounding stated
- [x] One potential bottleneck analyzed per core quality
