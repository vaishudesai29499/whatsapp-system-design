# WhatsApp-Like Messaging Platform — System Design

A production-oriented system design for a WhatsApp-like real-time messaging platform.

## What this project covers

- Real-time messaging with WebSockets
- Sender → server → receiver message flow
- Offline message delivery
- End-to-End Encryption (E2EE) concepts using the Signal Protocol
- Redis for presence and connection routing
- Message queues for asynchronous delivery
- Scalable message storage
- Delivery/read receipts
- Retry and idempotency
- Security and system-design trade-offs

## High-Level Architecture

```text
Users
  |
  v
Load Balancer
  |
  v
WebSocket Gateway
  |
  v
Message Service
  |---------------------> Redis
  |
  +---------------------> Message Queue
                              |
                              v
                       Delivery Workers
                         /          \
                        v            v
                 Online WebSocket   Push Service
                        |            |
                        v            v
                    Receiver      Offline Device

Message Service ---> Message Store
```

## Core Message Flow

1. Sender's device establishes a secure session with the receiver's device.
2. Sender encrypts the message locally.
3. Encrypted ciphertext is sent to the messaging backend.
4. WebSocket Gateway accepts the connection/message.
5. Message Service validates metadata and assigns/accepts a message ID.
6. If the receiver is online, the delivery service sends the ciphertext over the receiver's WebSocket.
7. If the receiver is offline, the encrypted message is queued/stored temporarily.
8. Receiver's device decrypts the ciphertext locally.
9. Delivery/read acknowledgements are sent back to the server.

## End-to-End Encryption

The important security boundary is:

```text
Sender Device -- encrypted ciphertext --> Server -- encrypted ciphertext --> Receiver Device
       |                                                                  |
       +---------------- encryption/decryption happens on endpoints ------+
```

The messaging server is designed to route and deliver ciphertext rather than needing the plaintext message.

Signal Protocol concepts relevant to this design include:

- X3DH/session establishment
- Prekeys for asynchronous session establishment
- Double Ratchet for evolving message keys
- Authenticated encryption
- Forward secrecy and related security properties

This repository is a system-design/educational implementation. It is not a claim to reproduce WhatsApp's proprietary implementation.

## Scalability

Potential components:

| Component | Responsibility |
|---|---|
| Load Balancer | Distribute client connections |
| WebSocket Gateway | Maintain real-time connections |
| Message Service | Validate and route messages |
| Redis | Presence and connection routing |
| Kafka/Queue | Durable asynchronous events |
| Message Store | Persist pending message ciphertext |
| Push Service | Notify offline devices |
| Object Storage | Store media objects |
| CDN | Deliver media efficiently |

## Key Design Decisions

### Why WebSockets?
Persistent bidirectional connections reduce the need for repeated polling and support low-latency message delivery.

### Why a message queue?
The queue decouples message acceptance from delivery and allows retries and worker scaling.

### Why Redis?
Presence and connection mappings are frequently read and updated and are well suited to low-latency in-memory storage.

### Why asynchronous media processing?
Large images/videos should not block the message path. Media can be uploaded to object storage and processed by background workers.

## Interview Topics

- WebSockets
- Load balancing
- Redis
- Kafka/message queues
- Database partitioning
- Idempotency
- Offline delivery
- E2EE
- Signal Protocol
- Forward secrecy
- Push notifications
- Failure handling
- Horizontal scaling

## Repository Structure

```text
whatsapp-system-design/
├── README.md
├── architecture/
│   ├── high-level-architecture.png
│   ├── message-flow.png
│   └── encryption-flow.png
├── docs/
│   ├── message-flow.md
│   ├── signal-protocol.md
│   ├── offline-messaging.md
│   ├── scalability.md
│   └── tradeoffs.md
└── diagrams/
```

## Disclaimer

This project is an independent system-design study. It is not affiliated with or an implementation of WhatsApp. Signal Protocol explanations are intentionally simplified for architecture and interview learning.
