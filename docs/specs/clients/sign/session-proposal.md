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
  Note over DS: engine.connect()
  Note over DS: pairing.create()
  Note over DS: generate pairing symkey and topic
  Note over DS: generate pairing URI<br>(symkey, topic, etc.)
  DS->>+R: subscribe to pairing topic
  Note over R: optimistic subscribe
  R-->>-DS: ACK subscribe
  Note over DS: ret pairing.create()
  Note over DS: generate key pair X
  Note over DS: prepare session proposal<br>(public key X, required + optional namespaces)
  DS->>+R: publish session proposal request to pairing topic
  R-->>-DS: ACK publish
  DS-->>-D: pairing URI
  Note over D: display pairing URI to user
  D->>+W: pairing URI

  Note over W: user scans pairing URI
  W->>+WS: engine.pair()
  WS->>+R: subscribe to pairing topic
  Note over R: optimistic subscribe
  R-->>-WS: ACK subscribe
  R->>WS: receive session proposal request
  WS->>W: emit session_proposal
  W->>WS: approve()
  Note over WS: generate key pair Y
  Note over WS: derive session symkey (pubX, privY) and topic
  WS->>+R: subscribe to session topic
  Note over R: optimistic subscribe
  R-->>-WS: ACK subscribe
  Note over WS: generate session settlement (public key Y)
  Note over WS: generate pairing response (public key Y)
  WS->>+R: publish pairing response to pairing topic
  R-->>-WS: ACK publish
  WS->>+R: publish session settlement request to session topic
  R-->>-WS: ACK publish
  WS-->>W: session_connect

  R->>+DS: receive pairing response
  Note over DS: derive session symkey (privX, pubY) and topic
  DS->>+R: subscribe to session topic
  Note over R: optimistic subscribe
  R-->>-DS: ACK subscribe
  R->>DS: receive session settlement request
  DS->>+R: publish session settlement response
  R-->>-DS: ACK publish
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
