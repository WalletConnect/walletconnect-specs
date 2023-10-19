# Notify Server API

## Authentication

All endpoints expect an `Authorization` header in the form `Authorization: Bearer <notify_api_secret>` using the notify_api_secret associated with a project ID. The secret used should be the one that was generated automatically when configuring notify - with the name `notify_api_secret`.

## Send Notifications

Send 1 or more notifications to accounts. Notifications handled itempotently are deduplicated by `notificationId`.

`POST /v1/<project-id>/notify`

```typescript
type Body = [{
  // Idempotency key and ID to track status
  notificationId?: string | null,
  notification: {
    // The notification type ID found in [Notify Config](../../clients/notify/notify-config.md)
    type: string,
    title: string,
    body: string,
    icon?: string | null,
    url?: string | null,
  },
  // The accounts to send this notification to
  accounts: AccountId[],
}]
```

```typescript
// CAIP-10 account ID
type AccountId = string
```

```typescript
type Response = {
  // IDs for the sent notifications returned in the same order as the body. If a `notificationId` wasn't specified in the request, one will be generated.
  notifications: string[],
}
```

## Notification Status

Get the curent status of a sent notification.

`GET /v1/<project-id>/notification/<notification-id>`

```typescript
type Response = {
  status: {
    [AccountId]: Status,
  },
}
```

```typescript
// The status of the notification. Client must be forwards-compatible with new statuses.
type Status = 
  // Notifications currently queued to be published
  "queued" |
  // Notifications successfully published to subscribers
  "published" |
  // Notifications not published becuase those accounts were not subscribers
  "not-subscribed" |
  // Notifications not published becuase while those accounts were subscribers, they were not subscribed to the sent notification type
  "wrong-scope" |
  // Notifications not published because the account's rate limit was hit
  "rate-limited"
}
```

## Subscribers 

Get the list of all accounts currently subscribed to this app.

`GET /v1/<project-id>/subscribers`

```typescript
type Response = AccountId[]
```

## Webhooks

Webhooks can be registered so your app can receive an HTTP request whenver an account subscribes or unsubscribes to notifications.

### Webhook Request

`POST <URL>`

```typescript
type Body = {
  // JSON WebhookPayload
  payload: string,
  // HMAC signature of `payload` equivilent to Stripe's verification logic: https://stripe.com/docs/webhooks#verify-manually
  signature: string,
}

type WebhookPayload = {
  // ID of the webhook that this request is for
  webhookId: string,
  // Unique ID of the event to deduplicate requests that were retried
  idempotencyKey: string,
  // Which event happened
  event: Event,
  // The account that triggered the event
  account: AccountId,
  // The new set of notification types of the account's subscription
  scope: string[],
}

type Event = "subscribed" | "updated" | "unsubscribed";
```

URL must return a 2xx status code, or the webhook request will be retried for 7 days with exponential backoff. Response body is ignored.

### Register Webhook

Register a webhook that will be invoked when accounts subscribe to notifications, update their notification subscription, or unsubscribe.

Each project is limited to a maximum of 5 webhooks.

`POST /v1/<project-id>/webhooks`

```typescript
type Body = {
  // Which events to listen for
  events: Event[],
  // HTTP endpoint to send Webhook Requests to
  url: string,
}
```

```typescript
type Response = {
  // Webhook ID
  id: string,
  // Shared secret for authenticating Webhook Requests
  secret: string,
}
```

### Registered Webhooks

Get the list of registered webhooks.

`GET /v1/<project-id>/webhooks`

```typescript
type Response = WebhookResponse[]

type WebhookResponse = Webhook & {
  // Webhook ID
  id: string,
  // Shared secret for authenticating Webhook Requests
  secret: string,
}
```

### Update Webhook

Update a webhook.

`PUT /v1/<project-id>/webhooks/<webhook_id>`

```typescript
type Body = {
  // Which events to listen for
  events: Event[],
  // HTTP endpoint to send Webhooks Requests to
  url: string,
}
```

No response.

### Rotate Webhook Shared Secret

Rotates the shared secret and returns the new secret to be installed on the endpoint. The old secret will continue to sign webhooks for for 24 hours, in addition to the new one.

`POST /v1/<project-id>/webhooks/<webhook_id>/secret`

No body.

```typescript
type Response = {
  // New shared secret for authenticating Webhook Requests
  secret: string,
}
```

### Delete Webhook

Delete a webhook.

This method is idempotent. If webhook ID does not exist, the request will still be successful.

`DELETE /v1/<project-id>/webhooks/<webhook_id>`

No response.

## Subscribe Topic

Used to generate a subscribe topic for an app to receive subscription requests from an account. Returns keys that should be stored on apps's domain a did:web document.

**Note:** this method is idempotent and will always return the same key.

`POST /v1/<project-id>/subscribe-topic`

```typescript
type Body = {
  appDomain: string,
}
``` 

```typescript
type Response = {
  subscribeKey: string,
  authenticationKey: string,
}
```

```mermaid
sequenceDiagram
    autonumber
    participant R as Relay
    participant N AS Notify
    participant D as Notify DB
    participant A as dApp

    %% Subscribe Topic
    activate A
    A->>+N: Call /subscribe-topic 
    Note over N: Generate privateKeyX
    Note over N: Generate identityKey
    N->>+D: Store privateKeyX, identityKey
    D-->>-N: ACK
    N->>+R: subscribe(sha256(publicKeyX))
    R-->>-N: ACK
    N->>-A: did.json { publicKeyX, identityKey }
    deactivate A
```
