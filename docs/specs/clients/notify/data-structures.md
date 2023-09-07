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
  "title": string,
  "body": string,
  "icon": string,
  "url": string,
  "type": string
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

## Notify Type

```jsonc
{
  "name": string,
  "description": string
}
```

## Notify Available Types

```jsonc
{
  "version": number,
  "lastModified": Int64,
  "types": NotifyType[]
}
```

## Notify Server Subscriptions

```jsonc
NotifyServerSubscription[]
```

`NotifyServerSubscription`:
```typescript
{
  topic: string, // dApp subscription topic to manage the subscription and call wc_notifySubscriptionUpdate and wc_notifySubscriptionDelete
  dappUrl: string, // dApp url that the subscription refers to
  account: Account, // CAIP-10 account
  scope: string[], // Array of notification types enabled for this subscription
  symKey: string, // Symetric key used for notification topic. sha256 to get notify topic
  expiry: number, // Unix timestamp of expiration
}
```
