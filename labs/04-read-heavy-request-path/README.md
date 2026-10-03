# Lab 4: Scale the Dashboard Read Path

## Goal

Apply [Lecture 5](../../lectures/lecture-05-horizontal-scaling-caching-and-availability.md)
to the Personal Investment Dashboard. Scale the service and the Market Data
Store, choose how requests reach ready instances, and design at least two cache
use cases with local memory and Redis.

Keep the quality requirements from Lab 2 and the system boundary from Lab 3.
Record any change to those earlier decisions.

## Given information

- The 3,000-User case creates 600 Stock price reads per second (RPS). Use a
  20% capacity buffer for this lab. Preserve that target after one Dashboard
  instance stops.
- One Dashboard instance safely handles 250 Stock price RPS while meeting its
  latency target. Assume this load test used the resource settings in Lecture
  5's HPA example. Stock price requests have similar service cost.
- The starting Market Data Store has one node. It can serve at most 400 public
  Stock price reads per second while meeting the latency target and accepting
  market-data writes. Each read replica can serve the same 400 read RPS.
  Reserve the write leader for accepted-data writes and reads that need its
  latest value. Do not count it toward routine public-read capacity.
- The Market Data Store persists accepted prices and provider times. A
  Market Data Provider can return delayed data, invalid data, rate limits, or
  no answer. Not every Browser read needs a new provider request.
- A local memory cache can lose any entry at any time. Redis can be empty or
  unavailable, including just before market open. Do not assume a cache hit
  rate when sizing the service or read replicas.
- Lab 2 also includes Overview, History, Filter, and Search reads. Cache a
  result across Users only if it contains no private User data.

These are teaching assumptions. In a real system, measure instance and store
capacity with representative load and failure tests.

## 1. Scale the service and route requests

1. Calculate the buffered Stock price target. Calculate the smallest number
   of ready Dashboard instances that preserves it after one instance stops.
2. Choose L4 or L7 balancing and one selection method: round robin, least
   connections, or hash by client IP. Explain why it fits these requests and
   what its main limit is.
3. State which data must survive the loss of one Dashboard instance and which
   temporary data may stay in that instance's memory.

## 2. Scale the Market Data Store

Re-use the diagram of the Dashboard's Container View and add the following elements to it:

- Load balancer
- Service instances
- Database instances

## 3. Cache two public reads

Use **Stock price** as the first case. Choose at least one more read from
Overview, History, Filter, or Search that contains no private User data. For
each case, explain the repeated work that caching removes and define a cache
contract:

| Decision | What to specify |
| --- | --- |
| Key and value | Include every input that changes the result; exclude private User data |
| Source | Name the stored data that can rebuild an empty cache |
| L1 lifetime | Give a short local lifetime that does not outlive the data policy |
| Freshness | Choose simple TTL or stale-while-revalidate; give fresh, stale, and expiry limits |
| Failure result | State what the User sees when either cache layer or its durable source fails |

Draw **one Mermaid cache-read sequence for each use case**. Each sequence must
show an L1 hit, an L1 miss followed by a Redis hit, and misses in both layers
followed by a durable-source read and cache fill. Also show an unavailable
source result.
If you choose stale-while-revalidate, show an allowed stale result and a
failed background refresh.

Explain which bounded keys you would pre-warm before market open and after
recovery. State what the User sees when warmup is incomplete or safe source
fallback capacity is full.

## 4. Implement the Redis cache path with commands

To run the commands locally, follow the official Redis
[installation guide](https://redis.io/docs/latest/operate/oss_and_stack/install/install-redis/)
for your system. The Redis [getting-started guide](https://redis.io/docs/latest/develop/get-started/)
shows how to start a server, and the [CLI guide](https://redis.io/docs/latest/develop/tools/cli/)
shows how to send commands. Check your connection with `redis-cli PING`;
Redis should return `PONG`.

For **each** cache use case, show a short `redis-cli` transcript. Use concrete
keys and values. Run these steps in order:

1. `GET` the key and show a miss.
2. Read the durable source in your sequence diagram. Then use `SET` with a
   sample JSON value and `EX` expiry in seconds to fill Redis.
3. `GET` the key again and use `TTL` to inspect its remaining lifetime.

Show the JSON value that you store. Keep `cached_at` and source times distinct
where they matter. Explain why a Redis TTL does not prove source-data
freshness. State whether your Redis design uses no disk persistence, RDB, or
AOF, and what happens after Redis restarts with an empty cache.

## What to submit

1. The Dashboard instance calculation, one load-balancer choice with its
   reason and limit, and the state that must survive an instance loss.
2. The updated Mermaid Container View from Lab 3. Show the load balancer,
   Dashboard service instances, and Market Data Store instances.
3. One cache contract and one Mermaid cache-read sequence for each chosen
   public read, with at least two use cases in total.
4. The bounded cache warmup and fallback rules, including the User result
   when warmup is incomplete or fallback capacity is full.
5. A `redis-cli` transcript for each cache use case, with JSON values, expiry
   commands, and your Redis persistence choice.

## Checklist

- [ ] My Dashboard instance calculation includes the buffer and one-instance
      loss.
- [ ] I chose a load-balancing layer and method, and explained the reason and
      a limit.
- [ ] My updated Container View shows the load balancer, service instances,
      and database instances. Required state survives a service instance loss.
- [ ] I have at least two cache use cases, with one contract and one sequence
      diagram for each, showing L1, Redis, and the durable source.
- [ ] My cache keys and values contain no private User data.
- [ ] My sequences show cache hits, misses, source failure, and any chosen
      stale-while-revalidate behavior.
- [ ] My warmup is bounded, and incomplete warmup or full fallback capacity
      has an explicit User-visible result.
- [ ] Each Redis transcript shows `GET` miss, `SET ... EX`, `GET` hit, and
      `TTL`, with a JSON value and a persistence choice.
- [ ] I explain why Redis TTL does not prove source-data freshness.
- [ ] My Mermaid diagrams render without an error.
