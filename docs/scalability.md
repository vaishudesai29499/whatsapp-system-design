# Scalability

## 1. Horizontal WebSocket Scaling

Do not depend on a single WebSocket server.

```text
                 Load Balancer
                 /     |      \
                v      v       v
             WS-1    WS-2    WS-3
```

Connections are distributed across gateway instances.

## 2. Presence

Use Redis or another low-latency distributed store for:

```text
user_id -> online/offline
user_id + device_id -> gateway_id
```

Presence should have expiration/heartbeat handling because network failures can leave stale connections.

## 3. Message Queue

A queue such as Kafka can decouple:

```text
Message Acceptance
       |
       v
     Queue
       |
       v
Delivery Workers
```

This allows worker capacity to scale independently.

## 4. Database Partitioning

At very large scale, message data can be partitioned by a stable key such as:

```text
conversation_id
```

The exact partition key should be selected based on access patterns and hotspot analysis.

## 5. Media

Do not send large media files through the core message service.

A better flow is:

```text
Client
  |
  v
Object Storage
  |
  v
Media Processing Queue
  |
  v
Workers
  |
  v
CDN
```

The chat message can contain encrypted/media metadata and a reference to the media object according to the application's E2EE design.

## 6. Backpressure

When traffic spikes:

```text
Incoming Messages
       |
       v
Queue grows
       |
       v
Workers scale
```

Monitor queue depth and processing latency.

## 7. Observability

Track:

- WebSocket connection count
- message throughput
- delivery latency
- queue depth
- retry rate
- database latency
- cache hit rate
- error rate
- reconnect rate

Do not log plaintext message content in application logs.
