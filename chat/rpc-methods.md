# RPC Methods

This doc should be used as a _source-of-truth_ and reflect the latest decisions and changes applied to the WalletConnect collection of client-to-client JSON-RPC methods for all platforms SDKs.

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

### wc_chatInvite

Used to invite a peer through topic I. Requires a success response with associated data

- Success response is equivalent to invite acceptance.
- Error response is equivalent to invite rejection.

```jsonc
// unencrypted invite struct
{
  "message": string,
  "account": string,
  "signature": string, // optional
}


// wc_chatInvite params
{
  "publicKey": string,
  "invite": string // encrypted invite struct
}
```

```jsonc
// Success result
{
  "publicKey": string, // invitee public key
}
```

### wc_chatMessage

Used to send a message to its peer through topic T.

- Success response is equivalent to message delivery receipt.
- Error response is equivalent to message delivery failure.

```jsonc
// wc_chatInvite params
{
  "publicKey": string,
  "message": string,
  "media": { // optional
    "type": string,
    "data": string,
  }
}
```

```jsonc
// Success result
{
  "receivedAt": Int64 // timestamp when received
}
```

### wc_chatPing

Used to evaluate if peer is currently online. Timeout at 30 seconds

```jsonc
// wc_chatPing params
{
  // empty
}
```

### wc_chatLeave

```jsonc
// wc_chatInvite params
{
  "publicKey": string,
}
```

```jsonc
// Success result
{
  "receivedAt": Int64 // timestamp when received
}
```
