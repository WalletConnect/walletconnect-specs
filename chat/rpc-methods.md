# RPC Methods

## Definitions

`Invite topic` - topic derived by hashing registry item pub_key or invite uri pub_key with sha256 
`Thread topic` - topic derived by hashing thread sym_key with sha256


This doc should be used as a _source-of-truth_ and reflect the latest decisions and changes applied to the WalletConnect Chat collection of client-to-client JSON-RPC methods for all platforms SDKs.


## Invite

Used to invite a peer through the invite topic.

### wc_chatInvite

```jsonc
// wc_chatInvite params
{
    "pubKey": string
    "openingMessage": string
}
```
`pubKey` - inviters public key used for DH key exchange to derive sym_key 
`opening_message` - opening message encrypted with sym_key

## Message

Used to send Chat messages through the thread topic.

### wc_chatMessage

```jsonc
// wc_chatMessage params
{
    "message": string
}
```

## Ping

### wc_chatPing

```jsonc

// wc_chatPing params
{
  // empty
}
```

## Leave

Used to communicate leaving the thread

### wc_chatLeave

```jsonc

// wc_chatLeave params
{
  // empty
}
