# RPC Methods

All methods follow the [JSON-RPC 2.0 spec](https://www.jsonrpc.org/specification).

All methods contain an "auth" field in the `params` (for request) or `result` (for response) which is a signed JWT. JWTs are specified in the [authentication](./notify-authentication.md) document.

## Methods

### wc_notifyWatchSubscriptions

Used to get the list of subscriptions for an account, and watch for updates.

**Request**

```jsonc
{
  "watchSubscriptionsAuth": string
}

| IRN     |          |
| ------- | -------- | 
| TTL     | 30       |
| Tag     | 4010     |
```

Topic: hash of key agreement public key from [Notify Server Authentication](./notify-server-authentication.md)

Message uses type 1 envelope with the client's persistant private key

**Response**

```jsonc
{
  "responseAuth": string
}

| IRN     |          |
| ------- | -------- |
| TTL     | 300      |
| Tag     | 4011     |
```

Topic: hash of symmetric key derivation of client's persistant private key and key agreement public key from [Notify Server Authentication](./notify-server-authentication.md)

### wc_notifySubscriptionsChanged

Used to indicate a change to subscriptions has occurred.

**Request**

```jsonc
{
  "subscriptionsChangedAuth": string
}

| IRN     |          |
| ------- | -------- | 
| TTL     | 300      |
| Tag     | 4012     |
```

Topic: same as wc_notifyWatchSubscriptions response

**Response**

```jsonc
{
  "responseAuth": string
}

| IRN     |          |
| ------- | -------- |
| TTL     | 300      |
| Tag     | 4013     |
```

Topic: same as wc_notifyWatchSubscriptions response

### wc_notifySubscribe

Used to subscribe notify subscription to a peer through subscribe topic. Response is expected on the response topic.

**Request**

```jsonc
{
  "subscriptionAuth": string
}

| IRN     |          |
| ------- | -------- | 
| TTL     | 30       |
| Tag     | 4000     |
```

Topic: notify topic

**Response**

```jsonc
{
  "responseAuth": string
}

| IRN     |          |
| ------- | -------- |
| TTL     | 2592000  |
| Tag     | 4001     |
```

Topic: notify topic

### wc_notifyMessage

Used to publish a notification message to a peer through notify topic. Response is expected on the same topic.

**Request**

```jsonc
{
  "messageAuth": string
}

| IRN     |          |
| ------- | -------- |
| TTL     | 2592000  |
| Tag     | 4002     |
```

Topic: notify topic

**Response**

```jsonc
{
  "receiptAuth": string
}

| IRN     |          |
| ------- | -------- |
| TTL     | 2592000  |
| Tag     | 4003     |
```

Topic: notify topic

### wc_notifyUpdate

Used to update a notify subscription with a new notify subscription, replacing an existing notify subscription through notify topic.

**Note:** this method is atomically performing two methods (wc_notifyDelete + wc_notifySubscribe)

**Request**

```jsonc
{
  "updateAuth": string
}

| IRN     |          |
| ------- | -------- | 
| TTL     | 30       |
| Tag     | 4008     |
```

Topic: notify topic

**Response**

```jsonc
{
  "responseAuth": string
}

| IRN     |          |
| ------- | -------- |
| TTL     | 2592000  |
| Tag     | 4009     |
```

Topic: notify topic

### wc_notifyDelete

Used to inform the peer to close and delete a notify subscription through notify topic. The reason field should be a human-readable message defined by the SDK consumer to be shown on the peer's side.

**Request**

```jsonc
{
  "deleteAuth": string 
}

| IRN     |          |
| ------- | -------- |
| TTL     | 2592000  |
| Tag     | 4004     |
```

Topic: notify topic

**Response**

```jsonc
{
  "responseAuth": string
}

| IRN     |          |
| ------- | -------- |
| TTL     | 2592000  |
| Tag     | 4005     |
```

Topic: notify topic
