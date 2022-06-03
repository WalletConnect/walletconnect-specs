# Chat Registry

Chat registry is a REST Api that allows chat users to register their blockchain accounts for public discoverability. 
In future it is worth to consider that registry records are stored on blockchain and remain fully controlled by account owner.

### Register
Used for registering a record with CAIP10 account.

`POST /register`

Body:
```jsonc
{
    “account”: string,
    “pubKey”: string
}
```

### Resolve
Used for resolving record by account.

`GET /resolve?account=string`

Response:
```jsonc
{
    “account”: string,
    “pubKey”: string
}
```
