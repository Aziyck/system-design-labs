# Lab 5: Model the Dashboard and Define Its API

## Goal

Describe the data model and HTTP API for the Personal Investment Dashboard. Then
generate OpenAPI from source definitions that match your design.

Read [Lecture 6](../../lectures/lecture-06-api-design-and-data-modeling.md). Use
the reviewed design from Labs 1-4.

```text
Data model -> endpoints -> source definitions -> OpenAPI -> client flow
```

## Scope

- Market overview, trending Stocks, filtering, and search by name or symbol.
- Stock detail, latest accepted prices, and daily price history.
- Reading a private Watchlist, adding a Stock, and removing a Stock.

Orders, Holdings, profit and loss, SSE, and public sharing are outside this lab.

## 1. Describe the data model

Complete the model before you design endpoints.

1. List the entities needed for market data and Watchlist. Include internal sync
   records if your earlier design needs them.
2. For each entity, define its purpose, identity, fields, and relationships.
   Give each field a meaning, example, and missing-value rule.
3. Draw a Mermaid ER diagram with relationship cardinality and ownership. Model
   the authenticated User as an external identity; no local User table or
   authentication endpoints are needed.
4. Separate stored facts from derived values, such as counts and freshness. Use
   **snake_case** for stored fields, **camelCase** for API fields, and direct
   boolean names such as `active`.

Keep source times and the freshness policy from earlier labs. Define how the
trending list is ranked. Use logical field meanings; SQL types, constraints, and
indexes are later work.

## 2. Describe all client endpoints

Map every action in the scope to a REST endpoint. One endpoint can cover several
actions through parameters.

| Product action | Method and versioned path | Parameters or body | Success | Access rule |
| -------------- | ------------------------- | ------------------ | ------- | ----------- |
|                |                           |                    |         |             |

For each operation, describe:

- Parameters and input fields: required values, defaults, and validation.
- Request and response models, with realistic JSON examples. Keep
  server-controlled fields out of input models.
- Success and error statuses, with one JSON error shape. Use `400` for invalid
  input.
- Authentication and access. Get the owner from authentication and check
  ownership on every private read or change.
- For lists: page-size bounds, ordering, filters, and next-page information.
  Show a filtered page and an empty result.

Record the shared API policies:

1. API version location, one compatible change, and one breaking change.
2. Authentication mechanism.
3. Pagination rules and client handling of `429` rate limits.

Describe Collector calls as server integrations. Cache copies do not need client
endpoints.

## 3. Generate and inspect OpenAPI

Use a framework such as FastAPI or NestJS with Swagger.

1. Define the routes, models, validation, errors, authentication, and examples
   in source code. Stub handlers are enough; mark them clearly.
2. Generate `openapi.yaml` or `openapi.json`. Fix mismatches in the source and
   regenerate.
3. Open the document in an OpenAPI viewer. Compare it with your endpoint
   inventory and check the examples, including a filtered page, an empty list,
   and an error.

Useful tools:

- [FastAPI: First Steps](https://fastapi.tiangolo.com/tutorial/first-steps/)
- [NestJS: OpenAPI introduction](https://docs.nestjs.com/openapi/introduction)
- [Swagger Editor](https://editor.swagger.io/)

## 4. Walk through a client flow

```text
Open trending Stocks -> open one Stock for more market data -> add it to Watchlist
```

List **all endpoints** needed for this flow, in call order. Use your API
examples to show the base URL, authentication, requests, and responses. Include
the market-data requests needed by the Stock detail screen.

State what the UI shows during loading, on success, for an empty list, and when
a request fails. No Browser implementation is required.

## What to submit

1. `SOLUTION.md`: data model, ER diagram, endpoint inventory, JSON examples, API
   policies, and client flow.
2. Source definitions and dependency information for OpenAPI generation.
3. Generated OpenAPI, the generation command, and a screenshot or short check
   record from the viewer.
