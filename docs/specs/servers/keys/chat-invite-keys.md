# Chat Invite Keys

## Register Invite Key

Used to register a new invite key. `idAuth` is did jwt of claims described under section [Key Authentication](./identity-keys.md)

`POST /invite`

### Body

```jsonc
{
    "idAuth": string
}
```

### Example

`POST https://keys.walletconnect.com/invite`

#### Body
```jsonc
{
  "idAuth": "eyJhbGciOiJFZERTQSIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJkaWQ6a2V5Ono2TWtrS3pHRHBRdjRtUjhHa2FtdDFXYnNyejRtRmpqUXBBZ0RGR0U5MTl2SDdUcyIsInN1YiI6ImVmZDA2MzlmYTVmYTdjYTMwODg4YzMzNzA2ZjZjMzk1ZTkyOGY0ZWM0NGQ3YmJlMzI2MmMzODc2NjhjY2Q4NDEiLCJhdWQiOiJodHRwOi8vMTAuMC4yLjI6ODA4MCIsImlhdCI6MTY3Mzk4NzU0NSwiZXhwIjoxNjc0MDczOTQ1LCJwa2giOiJkaWQ6cGtoOmVpcDE1NToxOjB4ZTk4MGNjZjkxMjRlODBjNDc3NDVkYjQwOTgyYzMwMWVkODM5ODAwZiJ9.AdgvaE52oI_Rg46QbYLO9hJP-pukcyLKbpzVShiwR4eUjVnxBhZ7-bJjT77Xa3sY6eCGrgOH1ARkby7H3tVzCg"
}
```

## Resolve Invite Key

Used to get an invite key for an account

- [Chat Invite Keys registration](../../clients/chat/invite-keys.md)

`GET /invite`

### Query Params

```jsonc
{
    "account": string,
}
```

### ResolveInviteKeyResponse
```jsonc
{
    "inviteKey": String
}
```

### Response

```jsonc
{
    "status": String,
    "error": ResponseError?,
    "value": ResolveInviteKeyResponse?
}
```

### Example

`GET https://keys.walletconnect.com/invite?account=eip155:1:0xe980ccf9124e80c47745db40982c301ed839800f`

#### Success Response
```jsonc
{
    "status": "SUCCESS",
    "error": null,
    "value": {
        "inviteKey": "did:key:z6LStTPuJjDfTJccKp7jKCbqZLkXP9QLbAeT925Th3kvzvzX"
    }
}
```

### Failure Response
```jsonc
{
    "status": "FAILURE",
    "error": {
        "name": "Invite key not found",
        "message": "Cannot find Invite key with specified identifier eip155:1:0xe980ccf9124e80c47745db40982c301ed839800f"
    },
    "value": null
}
```

## Remove Invite Key

Used to remove an invite key from the server. `idAuth` is did jwt of claims described under section [Key Authentication](./identity-keys.md)

`DELETE /invite`

### Body

```jsonc
{
    "idAuth": string
}
```

### Example

`DELETE https://keys.walletconnect.com/invite`

#### Body
```jsonc
{
  "idAuth": "eyJhbGciOiJFZERTQSIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJkaWQ6a2V5Ono2TWtrS3pHRHBRdjRtUjhHa2FtdDFXYnNyejRtRmpqUXBBZ0RGR0U5MTl2SDdUcyIsInN1YiI6ImVmZDA2MzlmYTVmYTdjYTMwODg4YzMzNzA2ZjZjMzk1ZTkyOGY0ZWM0NGQ3YmJlMzI2MmMzODc2NjhjY2Q4NDEiLCJhdWQiOiJodHRwOi8vMTAuMC4yLjI6ODA4MCIsImlhdCI6MTY3Mzk4NzU0NSwiZXhwIjoxNjc0MDczOTQ1LCJwa2giOiJkaWQ6cGtoOmVpcDE1NToxOjB4ZTk4MGNjZjkxMjRlODBjNDc3NDVkYjQwOTgyYzMwMWVkODM5ODAwZiJ9.AdgvaE52oI_Rg46QbYLO9hJP-pukcyLKbpzVShiwR4eUjVnxBhZ7-bJjT77Xa3sY6eCGrgOH1ARkby7H3tVzCg"
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
