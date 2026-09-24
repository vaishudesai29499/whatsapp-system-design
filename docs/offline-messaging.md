# Offline Messaging

Offline delivery is essential because mobile users frequently lose connectivity.

## Online path

```text
Sender
  |
  v
WebSocket Gateway
  |
  v
Message Service
  |
  v
Receiver WebSocket
  |
  v
Receiver
```

## Offline path

```text
Sender
  |
  v
Message Service
  |
  v
Message Queue / Pending Store
  |
  | receiver reconnects
  v
Delivery Worker
  |
  v
Receiver
```

## Delivery states

A message can move through states such as:

```text
ACCEPTED
   |
   v
SENT
   |
   v
DELIVERED
   |
   v
READ
```

The exact semantics should be explicitly defined.

## Retry

Delivery workers should retry transient failures.

Example:

```text
Attempt 1 -> failure
Attempt 2 -> failure
Attempt 3 -> success
```

Use exponential backoff to avoid overwhelming a recovering service.

## Deduplication

Retries can produce duplicate delivery attempts.

Use a unique message ID:

```text
message_id = 82731
```

The receiver or delivery layer can keep track of processed IDs.

## Expiration

Pending messages should not necessarily live forever.

A product can define a retention/expiration policy and remove expired ciphertext after the policy is satisfied.

## Multi-device

A user may have multiple devices.

The architecture should model:

```text
User
 ├── Device A
 ├── Device B
 └── Device C
```

The system must define how encrypted messages and device-specific cryptographic sessions are synchronized.
