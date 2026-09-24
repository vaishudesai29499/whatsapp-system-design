# Message Flow

## 1. Sender sends a message

Assume Alice sends Bob:

```text
Hello Bob
```

Alice's client first encrypts the plaintext using the established end-to-end encrypted session.

```text
Plaintext
   |
   v
Signal Protocol
   |
   v
Ciphertext
```

Only the ciphertext enters the normal messaging path.

## 2. Backend receives ciphertext

```text
Alice Device
     |
     | WebSocket
     v
Load Balancer
     |
     v
WebSocket Gateway
     |
     v
Message Service
```

The message can contain metadata such as:

```text
message_id
sender_id
receiver_id
encrypted_payload
timestamp
device_id
```

The payload is ciphertext.

## 3. Receiver is online

```text
Message Service
      |
      v
Delivery Service
      |
      v
Bob's WebSocket
      |
      v
Bob Device
      |
      v
Local Decryption
```

Bob sees the plaintext only after his device decrypts the message.

## 4. Receiver is offline

```text
Message Service
      |
      v
Queue / Pending Message Store
      |
      | Bob reconnects
      v
Delivery Worker
      |
      v
Bob Device
```

The server can retain encrypted pending messages according to the product's retention policy.

## 5. Acknowledgements

A useful state machine is:

```text
ACCEPTED -> SENT -> DELIVERED -> READ
```

A unique `message_id` allows the system to make retries idempotent and avoid processing the same message multiple times.

## 6. Failure handling

If the WebSocket disconnects:

1. Client reconnects.
2. Client authenticates again.
3. Client provides its last known synchronization point.
4. Server returns pending messages.
5. Client deduplicates using message IDs.
6. Client sends delivery acknowledgements.

The exact synchronization protocol can vary by implementation.
