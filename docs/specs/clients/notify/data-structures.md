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

```jsonc
{
  "title": string, // short message used in the title of the notification
  "body": string, // long messages ued in the body of the notification
  "icon": string, // image URL used to display with the notification
  "url": string, // redirect URL for call-to-action related to notification
  "type": string, // notification type which matches the scope of notify subscription
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
