# Push Server Spec

This is an overview spec document which details the endpoint and what they expect to receive and in turn what responses
you can expect.

## Security

The details of Push Server's Security are in [auth.md](./auth.md)

## Responses

These are the default responses and are only different if otherwise specified.

### Successful Response

```
{
  "status": "OK"
}
```

### Unsuccessful Response

```
{
  "status": "FAILED",
  "errors": [
    { "name": "", description": "" }
  ]
  "fields": [
    { "field": "type", "description": "", "location": "" }
  ]
}
```

This is sent whenever push server encounters an error. 
The `fields` property will only be available if it is a user provided field that caused the error. 
The `errors` property will only be available when we can provide more generic errors e.g. Postgres error.

> **Note** `location` should be treated as an enum (`body`, `query`, `header`, `path`) where `body` is the
> default unless otherwise specified.

## Requests

### Health

```
GET <PUSH_SERVER_URL>/health
```

#### Example Response

```
OK, push-server v0.1.0
```

### Register Client

```
POST <PUSH_SERVER_URL>/clients
```

Request body:
```typescript
{
  // The Client's ID from the Relay pairing.
  client_id: string,

  // The push service to use.
  type: "apns" | "apns-sandbox" | "fcm" | "noop",

  // The device's token for the push service.
  token: string,

  // See below
  always_raw?: boolean,
}
```

- `always_raw` - enables always sending of raw notifications
  - When set to `true`, the push notification will be `topic` and `message` from Send Notifications below.
  - When set to `false` or not present, the legacy behavior of conditionally sending (depending on `payload.flags`) encrypted or cleartext push notifications from `payload.blob` is used. The notification will contain `topic`, `flags`, and `blob` fields.

### Unregister Client

```
DELETE <PUSH_SERVER_URL>/clients/<CLIENT_ID>
```

- `CLIENT_ID`: The Client's ID from the Relay pairing.

### Send Notification

This API is called by the relay.

```
POST <PUSH_SERVER_URL>/clients/<CLIENT_ID>
```

- `CLIENT_ID` - This is used to map to the device token

Headers:
- `X-Ed25519-Timestamp` - timestamp of the signature
- `X-Ed25519-Signature` - hex encoded ed25519 signature of `format!("{}.{}.{}", timestamp, body.len(), body)` where `body` is the request body

Request body:
```typescript
{
  // Topic of the message. This is used by the SDKs to decrypt encrypted payloads on the client side
  topic: string,

  // The relay message.
  message: string,

  // ==== Legacy Fields ====

  // Unique ID for deduplication purposes
  // Note: When `message` is provided, `sha264(message)` should be used to deduplicate instead
  id: string,

  payload: {
    // Same as `topic` above
    topic: string,

    // See below
    flags: number,

    // See below
    blob: string,
  }
```

- `flags`: an integer value that uses bitmasks to encode information about the payload for the SDKs, the flags are below
  ```
  Encrypted  = 1 << 0
  Sign       = 1 << 1
  Auth       = 1 << 2
  Chat       = 1 << 3
  Notify     = 1 << 4
  ```
- `blob`: If the flags specify encrypted, then this is just the relay message. If not encrypted, this is cleartext and should follow the format from of the [Notify Message](https://specs.walletconnect.com/2.0/specs/clients/notify/data-structures#notify-message)
