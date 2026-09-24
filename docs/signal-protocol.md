# Signal Protocol and End-to-End Encryption

## Security Goal

For a two-person conversation, Alice should be able to send a message that Bob can decrypt while an intermediary server does not need to learn the plaintext.

Conceptually:

```text
Alice Device
    |
    | Encrypt locally
    v
Ciphertext
    |
    v
Messaging Server
    |
    | Route ciphertext
    v
Bob Device
    |
    | Decrypt locally
    v
Plaintext
```

## Session Establishment

Signal Protocol uses cryptographic mechanisms that allow parties to establish shared session secrets without sending the conversation's plaintext to the server.

A simplified conceptual flow is:

```text
Alice
  |
  | obtains Bob's public prekey material
  v
Bob's published public key material
  |
  v
Session establishment
  |
  v
Shared cryptographic state
```

The server can help distribute public key material/prekeys, but it should not need Bob's private key.

## Prekeys

Prekeys help a sender establish a secure session even when the receiver is not currently online.

This is important for mobile messaging because Bob may be offline when Alice starts a conversation.

## Double Ratchet

The Double Ratchet mechanism continuously evolves cryptographic keys.

Simplified:

```text
Message 1 -> Key 1
Message 2 -> Key 2
Message 3 -> Key 3
Message 4 -> Key 4
```

This is substantially safer than using one static encryption key for an entire conversation.

It supports security properties such as forward secrecy, subject to the protocol state and implementation.

## What the server can still know

End-to-end encryption protects message content, but it does not automatically make every piece of metadata invisible.

Depending on the product architecture, infrastructure may still process information such as:

- routing identifiers
- device identifiers
- timestamps
- delivery state
- connection information
- push-notification information

Therefore:

```text
E2EE != complete metadata privacy
```

## Important Implementation Warning

Do not implement your own cryptographic protocol for a production messaging system.

Use a well-reviewed protocol/library and follow its documented security model. This repository explains the architecture and security concepts; it is not a production cryptography implementation.
