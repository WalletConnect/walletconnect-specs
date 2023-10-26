# Data Structures

## Notify Subscription

```jsonc
{
  "topic": string,
  "account": string,
  "relay": {
    "protocol": string,
    "data": string
  },  
  "metadata": Metadata,
  "scope": Record<string, {description: string, enabled: boolean}>,
  "expiry": number,
}
```

## Notify Message

```typescript
{
  // Unique ID for the message
  id: string,
  // Notification type which matches the scope of notify subscription
  type: string,
  // Short message used in the title of the notification
  title: string,
  // Long messages used in the body of the notification
  body: string,
  // Image URL used to display with the notification. If empty, the app's icon from Notify Config is used instead
  icon: string,
  // Redirect URL for call-to-action related to notification. If empty, do not redirect
  url: string,
}
```

## Notify Message Record

```jsonc
{
  "id": string,
  "topic": string,
  "publishedAt": Int64,
  "message": NotifyMessage
}
```

## Notify Server Subscriptions

```jsonc
NotifyServerSubscription[]
```

`NotifyServerSubscription`:
```typescript
{
  appDomain: string, // dApp url that the subscription refers to
  symKey: string, // Symetric key used for notify topic. sha256 to get notify topic to manage the subscription and call wc_notifySubscriptionUpdate and wc_notifySubscriptionDelete
  account: Account, // CAIP-10 account
  scope: string[], // Array of notification types enabled for this subscription
  expiry: number, // Unix timestamp of expiration
}
```
