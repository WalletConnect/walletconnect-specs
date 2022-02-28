# JSON-RPC Methods
This doc should be used as a *source-of-truth* and reflect the latest decisions and changes applied to the WalletConnect collection of client-to-client JSON-RPC methods for all platforms SDKs.

## Definitions
- **Nullables:** Fields flagged as `Optional` can be ommited from the payload.
- Unless explicitly mentioned that a response requires associated data, all methods response's follow a default JSON-RPC pattern for the success and failure cases:
```json
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
-   Success response is equivalent to session approval.
-   Error response is equivalent to session rejection.
-   This method *might* require a special timeout, because it needs end-user interaction to respond.
```jsonc
// wc_sessionPropose params
{
    "relay": {
        "protocol": string,
        "data": string // Optional
    },
    "blockchainProposed": {
        "auth": string, // Optional
        "chains": [string] // CAIP-2 chain IDs
    },
    "permissions": {
        "jsonrpc": {
            "methods": [string]
        },
        "notifications": {
            "types": [string]
        },
        "controller": {
            "publicKey": string
        }
    },
    "proposer": {
        "publicKey": string,
        "controller": bool,
        "metadata": {
            "name": string,
            "description": string,
            "url": string,
            "icons": [string]
        }
    }
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

### wc_pairingExtend

### wc_pairingDelete

### wc_pairingPing

## Sessions:

### wc_sessionSettle

### wc_sessionRequest

### wc_sessionUpdate

### wc_sessionUpgrade

### wc_sessionExtend

### wc_sessionDelete

### wc_sessionPing

### wc_sessionNotify