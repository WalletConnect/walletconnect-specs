# JSON-RPC Methods
This doc should be used as a *source-of-truth* and reflect the latest decisions and changes applied to the WalletConnect collection of client-to-client JSON-RPC methods for all platforms SDKs.

## Definitions
- **Nullables:** Fields flagged as `Optional` can be ommited from the payload.
- Unless explicitly mentioned that a response requires associated data, all methods response's follow a default JSON-RPC pattern for the success and failure cases:
```jsonc
// Success
result: true

// Failure
error: {
    "code": number,
    "message": string
}
```

## Pairings:

### wc_sessionPropose
Used to propose a session through topic A. Requires a success response with associated data. 
- Success response is equivalent to session approval.
- Error response is equivalent to session rejection.
- This method *might* require a special timeout, because it needs end-user interaction to respond.
- Proposer must use the relay parameter selected and sent by the responder, if different than the proposed one.
```jsonc
// wc_sessionPropose params
{
    "relays": [
        {
            "protocol": string,
            "data": string // Optional
        },
    ],
    "proposer": {
        "publicKey": string,
        "metadata": {
            "name": string,
            "description": string,
            "url": string,
            "icons": [string]
        }
    },
    "chains": [string], // CAIP-2 chain IDs
    "methods": [string],
    "events": [string],
}
```

```jsonc
// Success result
{
    "relay": {
        "protocol": string,
        "data": string // Optional
    },
    "responder": {
        "publicKey": string
    }
}
```

### wc_pairingDelete
Used to inform the peer to close and delete a pairing. All associated sessions of the given pairing must also be deleted.
```jsonc
// wc_pairingDelete params
{
    "code": Int64,
    "message": string
}
```

### wc_pairingPing

## Sessions:

### wc_sessionSettle
Used to settle a session over topic B.
```jsonc
// wc_sessionSettle params
{
    "relay": {
        "protocol": string,
        "data": string // Optional
    },
    "controller": {
        "publicKey": string,
        "metadata": {
            "name": string,
            "description": string,
            "url": string,
            "icons": [string]
        }
    },
    "accounts": [string], // CAIP-10 account IDs
    "methods": [string], // json-rpc method names
    "events": [string], // event names
    "expiry": Int64, // seconds
}
```


### wc_sessionUpdateAccounts
```jsonc
// wc_sessionUpdateAccounts params
{
    "accounts": [string]
}
```
### wc_sessionUpdateMethods
```jsonc
// wc_sessionUpdateMethods params
{
    "methods": [string]
}
```
### wc_sessionUpdateEvents
```jsonc
// wc_sessionUpdateEvents params
{
    "events": [string]
}
```

### wc_sessionUpdateExpiry
Used to extend the lifetime of a session.
- The expiry is the absolute timestamp of the expiration date, in seconds.
```jsonc
// wc_sessionUpdateExpiry params
{
    "expiry": number
}
```

### wc_sessionDelete
Used to inform the peer to close and delete a session. The reason field should be a human-readable message defined by the SDK consumer to be shown on the peer's side.
```jsonc
// wc_sessionDelete params
{
    "code": Int64,
    "message": string
}
```

### wc_sessionPing

```jsonc
// wc_sessionPing params
{
    // empty
}
```


### wc_sessionRequest
Sends a CAIP-27 request to the peer client. The client should immediately reject the request and respond an error if the target session permissions doesn't include the requested method or chain ID.
```jsonc
// wc_sessionRequest params
{
    "request": {
        "method": string,
        "params": any
    },
    "chainId": string // Optional
}
```
### wc_sessionEvent
```jsonc
// wc_sessionEvent params
{
    "event": {
        "name": string,
        "data": any
    },
    "chainId": string // Optional
}
```

