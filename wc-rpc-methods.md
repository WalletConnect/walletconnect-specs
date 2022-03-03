# JSON-RPC Methods
This doc should be used as a *source-of-truth* and reflect the latest decisions and changes applied to the WalletConnect collection of client-to-client JSON-RPC methods for all platforms SDKs.

## Definitions
Unless explicitly mentioned that a response requires associated data, all methods response's follow a default JSON-RPC pattern for the success and failure cases:
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

### wc_pairingExtend

### wc_pairingDelete
Used to inform the peer to close and delete a pairing. All associated sessions of the given pairing must also be deleted.
```jsonc
// wc_pairingDelete params
{
    "code": int,
    "reason": string
}
```

### wc_pairingPing

## Sessions:

### wc_sessionSettle

### wc_sessionRequest

### wc_sessionUpdate

### wc_sessionUpgrade

### wc_sessionExtend

### wc_sessionDelete
Used to inform the peer to close and delete a session. The reason field should be a human-readable message defined by the SDK consumer to be shown on the peer's side.
```jsonc
// wc_sessionDelete params
{
    "code": int,
    "reason": string
}
```

### wc_sessionPing

### wc_sessionNotify
