## Request/response pattern

This is used for all CRUD actions including:

- Get subscriptions (including notification symkey)
  - If limited access, only give for domain
  - If unlimited access, give all subscriptions
- Create subscription
- Update subscription
- Delete subscription

It involves using a public request topic to send the message, and a generated response topic unique per caller.

The third topic notify uses is the notification topic for sending notifications exclusively, and this topic is retrieved via "List subscriptions` above.

```mermaid
sequenceDiagram
    autonumber
    participant U AS Wallet
    participant W AS SDK
    participant I AS Identity Server
    participant R as Relay
    participant N AS Notify
    participant D as Notify DB
    participant A as dApp

    %% Subscribe Topic
    activate A
    A->>+N: Create did.json
    Note over N: Generate privateKeyX
    Note over N: Generate identityKey
    N->>+D: Store privateKeyX, identityKey
    D-->>-N: ACK
    N->>+R: subscribe(sha256(publicKeyX))
    R-->>-N: ACK
    N->>-A: did.json { publicKeyX, identityKey }
    deactivate A

    activate W
    Note over W: Generate identityKey
    Note over W: Generate SIWE
    W->>+U: Sign SIWE
    U->>-W: Signature
    W->>+I: Store signed SIWE CACAO
    I-->>-W: ACK
    deactivate W

    %% Request
    activate U
    U->>+W: User subscribes to dapp
    Note over W: Generate publicKeyY
    Note over W: S = deriveSymmetric(privateKeyY, publicKeyX)
    Note over W: resTopic = sha256(S)
    W->>+R: subscribe(resTopic)
    R-->>-W: ACK
    W->>+A: Wallet fetches publicKeyX from the did:web document
    A->>-W: publicKeyX
    Note over W: reqTopic = sha256(publicKeyX)
    Note over W: notify_subscribe = signJwt(params, identityKey)
    W->>R: notify_subscribe + publicKeyY ON reqTopic
    activate R
    R->>N: fwd
    activate N
    R-->>W: ACK (ignored)
    N-->>R: ACK
    deactivate R
    N->>+D: get privateKeyX
    D-->>-N: ACK
    Note over N: S = deriveSymmetric(privateKeyX, publicKeyY)
    Note over N: resTopic = sha256(S)
    Note over N: req = decrypt(S, msg)
    N->>+I: Get SIWE for identity key
    I-->>-N: SIWE CACAO
    Note over N: authenticate(siwe, req) -- error if unauthorized
    Note over N: Generate P
    N->>+D: Store P @ domain,account
    Note over N,D: Error if sub already exists.
    D-->>-N: ACK
    Note over N: response = signJwt(P, identityKey)
    N->>+R: response ON resTopic
    activate R
    R->>W: fwd
    R-->>N: ACK
    deactivate N
    W-->>R: ACK
    deactivate R
    W->>+R: subscribe(sha256(P))
    R-->>-W: ACK
    W->>-U: UI updates
    deactivate U
```

"Prepare request" in subsequent diagrams include all steps prior to signing the JWT.

"Authenticate request" in subsequent diagrams include all steps in notify after receiving the message, until authenication.

## Create subscription

```mermaid
sequenceDiagram
    autonumber
    participant U AS Wallet
    participant W AS SDK
    participant I AS Identity Server
    participant N AS Notify
    participant D as Notify DB
    participant A as dApp

    activate U
    U->>+W: User opens app
    Note over W: Prepare request
    Note over W: notify_subscribe = signJwt(params, identityKey)
    W->>+N: notify_subscribe + publicKeyY ON reqTopic
    Note over N: Generate P
    N->>+D: Store P @ domain,account
    Note over N,D: Error if sub already exists.
    D-->>-N: ACK
    Note over N: response = signJwt(P, identityKey)
    N->>-W: response ON resTopic
    W->>+R: subscribe(sha256(P))
    R-->>-W: ACK
    W->>-U: UI updates
    deactivate U
```

## List subscriptions

```mermaid
sequenceDiagram
    autonumber
    participant U AS Wallet
    participant W AS SDK
    participant I AS Identity Server
    participant N AS Notify
    participant D as Notify DB
    participant A as dApp

    activate U
    U->>+W: User opens app
    Note over W: Prepare request
    Note over W: notify_list = signJwt(params, identityKey)
    W->>+N: notify_list + publicKeyY ON reqTopic
    N->>+D: Get P,scopes @ domain,account
    D-->>-N: data
    Note over N: response = signJwt((P,scopes), identityKey)
    N->>-W: response ON resTopic
    loop subscriptions
        W->>+R: subscribe(sha256(P))
        R-->>-W: ACK
    end
    W->>-U: UI updates
    deactivate U
```

## Update subscription

```mermaid
sequenceDiagram
    autonumber
    participant U AS Wallet
    participant W AS SDK
    participant I AS Identity Server
    participant N AS Notify
    participant D as Notify DB
    participant A as dApp

    activate U
    U->>+W: User edits subscription
    Note over W: Prepare request
    Note over W: notify_update = signJwt(params, identityKey)
    W->>+N: notify_update + publicKeyY ON reqTopic
    N->>+D: Update scopes @ domain,account
    Note over N,D: Error if sub doesn't exists.
    D-->>-N: ACK
    Note over N: response = signJwt((), identityKey)
    N->>-W: response ON resTopic
    W->>-U: UI updates
    deactivate U
```

## Delete subscription

```mermaid
sequenceDiagram
    autonumber
    participant U AS Wallet
    participant W AS SDK
    participant I AS Identity Server
    participant N AS Notify
    participant D as Notify DB
    participant A as dApp

    activate U
    U->>+W: User unsubscribes
    Note over W: Prepare request
    Note over W: notify_delete = signJwt(params, identityKey)
    W->>+N: notify_delete + publicKeyY ON reqTopic
    N->>+D: Delete @ domain,account
    Note over N,D: Error if sub doesn't exists.
    D-->>-N: ACK
    Note over N: response = signJwt(P, identityKey)
    N->>-W: response ON resTopic
    W->>+R: unsubscribe(sha256(P))
    R-->>-W: ACK
    W->>-U: UI updates
    deactivate U
```
