# Session Proposal

## User Flow

User visits application that requires to connect wallet

1. App displays qrcode or deep link to connect wallet
2. User scans qrcode or redirects to wallet
3. User approves session proposal from App
4. User returns to app after success prompt
5. App receives accounts and namespaces from Wallet
6. App sends request to sign transaction or message
7. User redirects to wallet to inspect request
8. User approves transaction or message request
9. User returns to app after success prompt
10. App receives transaction confirmation or signature

## Proposal Protocol

#### Prerequisites
Wallet and Dapp are required to establish pairing P before proceeding to Sign protocol execution.


#### Protocol

Proposal protocol will be established as follows:

1. Dapp sends session proposal on pairing P with publicKey, relay, permissions and metadata
2. Wallet receives session proposal with required namespaces and public key X on pairing P
3. Wallet generates key pair Y
4. Wallet settles session with symmetric key derived with keys X and Y
5. Session topic B key is derived from sha256 hash of session's symmetric key 
6. In parallel:
  * Wallet sends settlement payload to topic B with metadata, namespaces and public key Y
  * Wallet sends proposal response on pairing P with publicKey Y 
7. Dapp receives propose response on pairing P
8. Dapp settles session with topic B using derived key
9. Dapp receives settlement on topic B
10. Dapp sends acknowledgment on topic B

Note: numbers below are different from above

```mermaid
sequenceDiagram
  autonumber
  participant D AS dApp
  participant DS AS dApp Sign client
  participant R as Relay
  participant WS AS Wallet Sign client
  participant W as Wallet

  activate D
  D->>+DS: User clicks connect
  DS->>+R: SDK connects to relay<br>Time: TLS connection
  R-->>-DS: connected
  DS->>+R: WebSocket upgrade<br>Time: network latency
  R-->>-DS: complete
  Note over DS: engine.connect()
  Note over DS: pairing.create()
  Note over DS: generate pairing symkey and topic
  Note over DS: generate pairing URI<br>(symkey, topic, etc.)
  DS->>+R: subscribe to pairing topic<br>Time: network latency
  Note over R: optimistic subscribe<br>Time: 0ms
  R-->>-DS: ACK subscribe<br>Time: network latency
  Note over R: IRN: poll mailbox
  Note over R: IRN: check subscriber count
  Note over R: IRN: add subscription
  Note over DS: ret pairing.create()
  Note over DS: generate key pair X
  Note over DS: prepare session proposal<br>(public key X, required + optional namespaces)
  DS->>+R: publish session proposal request to pairing topic<br>Time: network latency
  Note over R: IRN: get subscribers (0)
  Note over R: IRN: get mailbox size
  Note over R: IRN: store in mailbox
  Note over R: Time: 3 IRN ops
  R-->>-DS: ACK publish<br>Time: network latency
  DS-->>-D: pairing URI
  Note over D: display pairing URI to user
  D->>+W: pairing URI

  Note over W: user scans pairing URI
  W->>+WS: engine.pair()
  WS->>+R: SDK connects to relay<br>Time: TLS connection
  R-->>-WS: connected
  WS->>+R: WebSocket upgrade<br>Time: network latency
  R-->>-WS: complete
  WS->>+R: subscribe to pairing topic<br>Time: network latency
  Note over R: optimistic subscribe<br>Time: 0ms
  R-->>-WS: ACK subscribe<br>Time: not critical path
  Note over R: IRN: poll mailbox
  Note over R: Time: 1 IRN ops
  R->>WS: receive session proposal request<br>Time: network latency
  Note over R: IRN: check subscriber count
  Note over R: IRN: add subscription
  WS->>W: emit session_proposal

  W->>WS: approve()
  Note over WS: generate key pair Y
  Note over WS: derive session symkey (pubX, privY) and topic
  WS->>+R: subscribe to session topic<br>Time: network latency
  Note over R: optimistic subscribe<br>Time: 0ms
  R-->>-WS: ACK subscribe<br>Time: network latency
  Note over R: IRN: poll mailbox
  Note over R: IRN: check subscriber count
  Note over R: IRN: add subscription
  Note over WS: generate session settlement (public key Y)
  Note over WS: generate session proposal response (public key Y)
  WS->>+R: publish session settlement request to session topic<br>Time: network latency
  Note over R: IRN: get subscribers (0)
  Note over R: IRN: get mailbox size
  Note over R: IRN: store in mailbox
  Note over R: Time: 2 IRN op
  R-->>-WS: ACK publish<br>Time: network latency
  WS->>+R: publish session proposal response to pairing topic<br>Time: network latency
  Note over R: IRN: get subscribers (1)
  Note over R: Realtime delivery
  Note over R: IRN: get mailbox size
  Note over R: IRN: store in mailbox
  Note over R: Time: min (1 IRN op +<br>2x dapp network latency, 2 IRN op)
  R-->>-WS: ACK publish<br>Time: network latency
  WS-->>W: session_connect

  DS->>+R: SDK connects to relay<br>Time: TLS connection
  R-->>-DS: connected
  DS->>+R: WebSocket upgrade<br>Time: network latency
  R-->>-DS: complete
  R->>+DS: receive session proposal<br>Time: network latency
  Note over DS: derive session symkey (privX, pubY) and topic
  DS->>+R: subscribe to session topic<br>Time: network latency
  Note over R: optimistic subscribe<br>Time: 0ms
  R-->>-DS: ACK subscribe<br>Time: not critical path
  Note over R: IRN: poll mailbox
  Note over R: Time: 1 IRN op
  R->>DS: receive session settlement request<br>Time: network latency
  Note over R: IRN: check subscriber count
  Note over R: IRN: add subscription
  Note over DS: TODO what is exact behavior & timing here?
  DS->>+R: publish session settlement response<br>Time: network latency
  R-->>-DS: ACK publish<br>Time: network latency
  DS-->>-D: connected
  deactivate D

  R->>WS: receive session settlement response
  WS->>W: session_approve
  deactivate WS
  deactivate W

  activate D
  D->>+DS: user requests signature
  Note over DS: TODO
```

```mermaid
stateDiagram-v2
  SP: Session proposed
  PR: Proposal received
  S: Settled
  [*] --> SP
  SP --> PR: scans QR code
  PR --> S
  S --> [*]
```
