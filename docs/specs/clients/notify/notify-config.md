# Notify Configuration

In order to enable notify, dapps must expose a static json file named `wc-notify-config.json` in their `.well-known` path under their domain URL.

The file should include the following information:

- schemaVersion - schema version number, should always be 1
- name - name of your app
- description - description of your app
- icons - array of icons to use. Different icons may be in different formats
- types - array of available `NotificationType`s

For example:

```jsonc
https://app.example.com/.well-known/wc-notify-config.json

// wc-notify-config.json
{
  "schemaVersion": 1,
  "name": "My app",
  "description": "My awesome app",
  "icons": ["https://i.imgur.com/q9QDRXc.png"],
  "types": [
    {
      "id": "promotional",
      "name": "Promotional",
      "description": "Get notified when new features or products are launched"
    }, {
      "id": "transactional",
      "name": "Transactional",
      "description": "Get notified when new on-chain transactions target your account"
    }, {
      "id": "private",
      "name": "Private",
      "description": "Get notified when new updates or offers are sent privately to your account"
    }, {
      "id": "alerts",
      "name": "Alerts",
      "description": "Get notified when urgent action is required from your account"
    }
  ]
}
```

## Notification Types

Not every message is the same and not every wallet user wants to receive every possible notification that a dapp targets their wallet account. Notification Types define a scope of messages that the user authorizes the dapp to notify the user related to their wallet account.

A notification type has 3 fields:
```typescript
type NotificationType = {
  // Non-changing ID used to keep track of which notification types are currently subscribed to. Can contain lowercase letters and underscores and must match the regex /^[a-z0-9_-]{2,32}$/
  id: string,
  // Human-readable name of the notification type
  name: string,
  // Human-readalbe description of what type of notifications this entails
  description: string,
}
```

### Subscription Scope

A Notify Subscription will include a claim labelled `scp` with a string value include the user authorized notification types separated by whitespaces.

For example:

```typescript
// did-jwt
{
  // .. other claims
  "scp": "promotional private alerts"
}
```

### Notify Message

A Notify Message will include a parameter called `type` in the message payload which indicates the notification type is being delivered to the wallet user

```typescript
{
  title: string,
  body: string,
  icon: string,
  url: string,
  type: string // <--- notification type ID
}
```
