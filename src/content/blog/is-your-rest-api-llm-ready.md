---
title: 'Is Your REST API LLM-Ready?'
description: "APIs weren't designed with AI agents in mind. But they're increasingly what's calling your endpoints. Here's what makes an API truly agent-safe."
pubDate: 'Mar 14 2026'
---

🤖 APIs weren't designed with AI agents in mind. But they're increasingly what's calling your endpoints. Here's what makes an API truly agent-safe:

## 01 — SCHEMA - Self-describing contracts

Agents can't read docs. Your OpenAPI schema IS the contract — and it must be accurate, complete, and machine-readable.

```
# Bad — vague, undiscoverable
{ "status": "ok" }

# Good — explicit enum in schema
{ "status": "pending" | "active" | "cancelled" }
```

## 02 — SAFETY - Idempotency by design

Agents retry on failure — often without knowing if the first call succeeded. Idempotency keys prevent duplicate actions.

```
POST /orders
Idempotency-Key: order-abc-7f3a

# Calling this 3x creates exactly 1 order.
# Safe to retry after a timeout.
```

## 03 — ERRORS - Actionable error messages

A human interprets a vague 400. An agent cannot. Tell the agent exactly what went wrong and what to do next.

```
# Bad
{ "error": "Bad request" }

# Good
{ "error": "INVALID_DATE_RANGE",
  "field": "end_date",
  "hint": "end_date must be after start_date" }
```

## 04 — RATE LIMITS - Graceful backoff signals

Agents call APIs in loops. Without clear backoff signals, they'll hammer your service on a 429.

```
# Response headers on 429
X-RateLimit-Limit:     1000
X-RateLimit-Remaining: 0
Retry-After:           30  # seconds
```

## 05 — AUTH - Scoped, least-privilege tokens

An agent booking a meeting shouldn't have access to billing. Fine-grained scopes limit blast radius. Use fine-grained OAuth scopes or API key permissions as well as short-lived tokens with refresh mechanics.

## 06 — SIDE EFFECTS - Dry-run / preview mode

Let agents simulate before they commit. A dry-run flag shows what would happen — without doing it. Make sure to include clear documentation of cascading effects (e.g. "deleting a project deletes all its tasks") and make use of Webhooks or event streams so the agent can observe outcomes asynchronously.

```
POST /invoices/send
{ "invoice_id": "inv_99", "dry_run": true }

# Returns what would be sent, to whom,
# and any warnings — without sending anything.
```

## 07 — PAGINATION - Cursor-based pagination

Offset pagination breaks when records are added mid-fetch. Cursors give agents a stable, resumable position.

```
# Bad — breaks with concurrent writes
GET /events?page=3&limit=50

# Good — stable across mutations
GET /events?after=evt_a8f3c&limit=50
```

## What about security?

It could be argued that making an API too transparent might increase its attack surface. This concern is real:

- **Verbose error messages** that reveal internal structure — e.g. "column user_id not found in table accounts" leaks your schema to anyone probing your API
- **Dry-run endpoints** that can be abused to enumerate valid IDs, emails, or resource states without leaving a trace in your audit logs
- **Rich OpenAPI schemas** published publicly that give attackers a complete map of every endpoint, parameter, and data type — essentially handing them a recon report
- **Idempotency key patterns** that are predictable could let an attacker replay or collide with legitimate requests

**But the framing matters a lot**. The transparency principles in this post are mostly about what the API tells authenticated, authorised callers — not what it broadcasts to the world. The distinction is:

> Transparency to the agent ≠ transparency to the public

An agent operating with a scoped token should get rich, actionable feedback within the boundaries of what it's allowed to see. A 403 can still say "you don't have calendar:write scope" without revealing anything sensitive. A dry-run can validate a request without exposing other users' data.

The real design principle is: transparency should be scoped to the caller's authorisation level. Which actually reinforces point 5 in this post — fine-grained auth isn't just about limiting what an agent can do, it's also about limiting what it can learn.

## Summary

Standard REST APIs are designed for humans who read docs and apply judgment. Agent-safe APIs are designed for callers that are fast, tireless, and literal.

The bar is higher — but the primitives are the same. You're just being more explicit about guarantees you should have made anyway.

What would you add to this list?

#APIDesign #AIAgents #SoftwareEngineering #LLMs #BackendDev