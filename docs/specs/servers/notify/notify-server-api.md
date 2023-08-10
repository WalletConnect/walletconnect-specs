# Notify Server API

## Authentication

All endpoints expect an `Authorization` header in the form `Authorization: Bearer <project_secret>` using the project secret associated with a project ID. The secret used should be the one that was generated automatically when configuring notify - with the name `notify_subscribe_topic_private_key`

## Notify

Used to notify a message to a set of accounts

`POST /notify`

Body:

```typescript
{
  "notification": {
    "title": string,
    "body": string,
    "icon": string,
    "url": string,
    "type": string
  },
  "accounts": string[]
}
``` 

Response: 

```typescript
{
  "sent": string[],
  "failed": Failed[],
  "notFound": string[]
}
```

Failed:

```typescript
{
  "account": string,
  "reason": string
}
```

## Subscribers 

Returns the list of all accounts currently subscribed to this dapp.

`GET /subscribers`

Response:

```typescript
{
  []string
}
``` 

## Subscribe Topic

Used to generate a subscribe topic for a dapp to receive push subscriptions, returns a public key and identity key that should be stored on dapps's domain a did:web document. Requires the dapps url to be sent in the body.

**Note:** this method is idempotent and will always return the same key.

`POST /subscribe-topic`

Body:

```typescript
{
  "dappUrl": string
}
``` 

Response:

```typescript
{
  "identityPublicKey": string,
  "subscribeTopicPublicKey": string 
}
``` 

Failed:

```typescript
{
  "reason": string
}
```

## Register Webhook

Used to register a webhook that would return when accounts are subscribed or unsubscribed

`POST /register-webhook`

Body:

```typescript
{
  "events": string[], // subscribed or unsubscribed
  "webhook": string
}
```

Response:

```typescript
{
  "id": string
}
```

Webhook payload:

```typescript
{
  "id": string,
  "event": string, // subscribed or unsubscribed
  "account": string, // CAIP-10 account
  "dappUrl": string // dApp's URL with which the account was registered
}
```


## Registered Webhooks

Used to retrieve the list of registered webhooks

`GET /webhooks`

Response:

```typescript
{
  "<webhook_id1>": {
    "url": "<webhook_url1>",
    "events": [
      "<event1>",
      "<event2>",
      ...
    ]
  },
  "<webhook_id2>": {
    "url": "<webhook_url2>",
    "events": [
      "<event1>",
      ...
    ]
  },
  ...
}
```


## Update Webhook

Used to update the registered webhook

`PUT /webhooks/<webhook_id>`

Body:

```typescript
{
  "events": string[], // subscribed or unsubscribed
  "webhook": string
} 
```

## Delete Webhook

Used to delete the registered webhook

`DELETE /webhooks/<webhook_id>`