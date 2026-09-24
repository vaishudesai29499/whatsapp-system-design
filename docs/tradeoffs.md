# Trade-offs

## WebSocket vs Polling

### WebSocket
Pros:
- Low latency
- Bidirectional
- Good for real-time messaging

Cons:
- Long-lived connections
- Connection management is more complex

### Polling
Pros:
- Simpler infrastructure

Cons:
- Higher unnecessary traffic
- Higher latency
- Poor fit for large-scale real-time chat

For the primary chat channel, WebSockets are a strong fit.

## SQL vs NoSQL

SQL can be useful when strong transactional semantics and relational queries are important.

NoSQL can be useful for very large message volumes and predictable key-based access.

The choice should follow workload, consistency, query patterns, operational maturity, and scale requirements rather than a blanket rule.

## Redis vs Database

Redis is useful for hot, short-lived state such as:

- presence
- connection routing
- rate limits

The durable database remains the source of truth for persistent application data.

## Kafka vs Direct Delivery

Direct delivery can be simpler at small scale.

A queue becomes useful when you need:

- buffering
- retries
- independent worker scaling
- asynchronous processing
- event pipelines

## Synchronous vs Asynchronous

Keep the latency-sensitive path small:

```text
Validate
 -> persist/accept
 -> enqueue
 -> deliver
```

Move non-critical work such as analytics and media processing to asynchronous workers.

## E2EE vs Server-Side Features

E2EE limits what the server can inspect in message content.

That improves content confidentiality but makes some server-side features harder to implement.

For example, server-side plaintext search cannot simply inspect encrypted message content.

The architecture therefore needs to decide which features run on-device versus on trusted infrastructure.

## Security vs Observability

Never solve observability by logging plaintext messages.

Prefer:

- message IDs
- latency
- status codes
- queue metrics
- error categories
- aggregate statistics

without collecting message content.
