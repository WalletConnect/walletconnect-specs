# Identity Keys

Identity Keys are used to verify blockchain account ownership and validating peer to peer requests are legitimate without requiring the wallet user to sign every message with their blockchain private key. By the blockchain account signing a CAIP-122 message that includes the Identity Key's public key, it authorizes the Identity Key to sign messages on behalf of the blockchain account.

```mermaid
sequenceDiagram
  autonumber
  participant U AS Wallet
  participant C AS Client
  participant I AS Keys Server

  activate C
  Note over C: Generate Identity Key
  Note over C: Generate CAIP-122 msg
  C->>+U: Request to sign CAIP-122 msg
  U->>-C: Signature
  C->>+I: Register CACAO
  I-->>-C: ACK
  deactivate C
```

## Keys

Identity Keys are randomly generated ed25519 key pairs that are unique per client/device per blockchain account.

If the wallet user uses multiple devices, each device SHOULD use a different identity key.

If the wallet user has multiple blockchain accounts, each account MAY use a separate identity key.

## Key Authorization

Identity Keys are authorized by a blockchain account by the wallet user signing a [CAIP-122](https://github.com/ChainAgnostic/CAIPs/blob/main/CAIPs/caip-122.md) message with includes the public key of the Identity Key as the URI.

Below is an example [CAIP-122](https://github.com/ChainAgnostic/CAIPs/blob/main/CAIPs/caip-122.md) message authorizing the Identity Key with the `z6MkqJ6qV18zBazggzhGMHNgadEQGbX9RceEH3j2G6kNTbKq` public key.

```
app.example.com wants you to sign in with your Ethereum account:
0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2

I further authorize this app to send and receive messages on my behalf for THIS domain using my WalletConnect identity. Read more at https://walletconnect.com/identity

URI: did:key:z6MkqJ6qV18zBazggzhGMHNgadEQGbX9RceEH3j2G6kNTbKq
Version: 1
Chain ID: 1
Nonce: bb0b6514e8a5e817
Issued At: 2023-09-01T15:29:36.509Z
Resources:
- https://keys.walletconnect.com
```

The important components from this message are as follows:
- `domain` - `app.example.com`
- `address` - `0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2`
- `Identity Key public key` - `z6MkqJ6qV18zBazggzhGMHNgadEQGbX9RceEH3j2G6kNTbKq`
- `statement` - `I further authorize this app to send and receive messages on my behalf for THIS domain using my WalletConnect identity. Read more at https://walletconnect.com/identity`

## Authorization levels

In the above example, "limited authorization" was used. Various forms of authorization are supported to enable various use cases, and to ensure that the user ultimately decides what permission an app has over their identity. The `statement` decides the authorization level.

## Limited statement

Limited authorization allows the app to only access messages for `domain`.

```text
I further authorize this app to send and receive messages on my behalf for THIS domain using my WalletConnect identity. Read more at https://walletconnect.com/identity
```

## Unlimited statement

Unlimited authorization allows the app to access messages, regardless of `domain`.

```text
I further authorize this app to send and receive messages on my behalf for ALL domains using my WalletConnect identity. Read more at https://walletconnect.com/identity
```

## CACAO Format

The CACAO is a standard JSON representation of a signed CAIP-122 message. For example:

```
{
  "h": {
    "t": "eip4361"
  },
  "p": {
    "aud": "did:key:z6MkqJ6qV18zBazggzhGMHNgadEQGbX9RceEH3j2G6kNTbKq",
    "iat": "2023-09-01T17:09:21.481+03:00",
    "statement" : "I further authorize this app to send and receive messages on my behalf for ALL domains using my WalletConnect identity. Read more at https://walletconnect.com/identity",
    "iss": "did:pkh:eip155:1:0xBAc675C310721717Cd4A37F6cbeA1F081b1C2a07",
    "nonce": "bb0b6514e8a5e817",
    "domain": "app.example.com",
    "version": "1",
    "resources": ["https://keys.walletconnect.com"],
  },
  "s": {
    "s": "5ccb134ad3d874cbb40a32b399549cd32c953dc5dc87dc64624a3e3dc0684d7d4833043dd7e9f4a6894853f8dc555f97bc7e3c7dd3fcc66409eb982bff3a44671b",
    "t": "eip191"
  }
}
```

## Key Registration

When two clients are using a peer to peer API for some requests they need to verify each others Identity Keys. Therefore we use the Keys Server to index these keys privately and the counter-party can validate that the key would be used for the corresponding account in the WalletConnect identities.

`POST /identity`

### Body

```jsonc
{
  "cacao": Cacao
}
```

### Example

`POST https://keys.walletconnect.com/identity`

#### Body
```jsonc
{
  "cacao": {
    "h": { ... },
    "p": { ... },
    "s": { ... }
  }
}
```

## Resolve Identity Key

Used to get a CACAO matching an Identity Key.

- [Notify Authentication](../../notify/authentication.md)
- [Chat Authentication](../../chat/chat-authentication.md)

`GET /identity`

### Query Params

```jsonc
{
  "publicKey": string,
}
```

### ResolveIdentityKeyResponse
```jsonc
{
  "cacao": Cacao
}
```

### Response

```jsonc
{
  "status": String,
  "error": ResponseError?,
  "value": ResolveIdentityKeyResponse?
}
```

### Example

`GET https://keys.walletconnect.com/identity?publicKey=z6MkkKzGDpQv4mR8Gkamt1Wbsrz4mFjjQpAgDFGE919vH7Ts`

#### Success Response
```jsonc
{
  "status": "SUCCESS",
  "error": null,
  "value": {
    "cacao": {
      "h": { ... },
      "p": { ... },
      "s": { ... }
    }
  }
}
```

#### Failure Response 
```jsonc
{
  "status": "FAILURE",
  "error": {
    "name": "Identity key not found",
    "message": "Cannot find Identity key with specified identifier z6MkkKzGDpQv4mR8Gkamt1Wbsrz4mFjjQpAgDFGE919vH7Ts"
  },
  "value": null
}
```


## Remove Identity Key

Used to remove an Identity Key from the server

`DELETE /identity`

### Body

```jsonc
{
  "idAuth": string
}
```

#### IdAuth

The `idAuth` is a JWT signed by the Identity Key with the following claims:

* iat - timestamp when JWT was issued 
* exp - timestamp when JWT must expire
* iss - public Identity Key in form of did:key
* aud - key server URL used for registering
* pkh - corresponding blockchain account (did:pkh)
* act - description of action intent. Must be equal to "unregister_identity"

### Example

`DELETE https://keys.walletconnect.com/identity`

#### Body
```jsonc
{
  "idAuth": "eyJhbGciOiJFZERTQSIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJkaWQ6a2V5Ono2TWtoQk5tQXZ0VDNxeW5YekVCcmlSa2o4TWJoTURxMUs1aFhpY1BTSlZTWnBzOSIsImF1ZCI6Imh0dHA6Ly8xMC4wLjIuMjo4MDgwIiwiaWF0IjoxNjc3MTg4NzU1MjUwLCJleHAiOjE2Nzk3ODA3NTUyNTAsInBraCI6ImRpZDpwa2g6ZWlwMTU1OjE6MHhiYjU0NjZkODcyZGQxN2Y4MDRkMjYwNDVhMWVkMDk3ZWQ4NDM2MmI3In0.cTqEuxARCPwewx4PqhUiG8BkerqGX18icRhMJr9PPQW8fFTqKgjV7D2vzjKcxAEUWY2BL4b2998V0jEASSEIAQ"
}
```

## Data structures

### ResponseError
```jsonc
{
  "name": String,
  "message": String
}  
```
