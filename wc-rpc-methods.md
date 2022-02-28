# JSON-RPC Methods
This doc should be used as a *source-of-truth* and reflect the latest decisions and changes applied to the WalletConnect collection of client-to-client JSON-RPC methods for all platforms SDKs.

## Definitions
Unless explicitly mentioned that a response requires associated data, all methods response's follow a default JSON-RPC pattern for the success and failure cases:
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