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
  "scope": Record<string, NotifyNotificationType>,
  "expiry": number,
}
```

## Notify Notification

```typescript
{
  // ID of the notification
  id: string,
  // Unix ms timestamp when the notification was sent
  sent_at: number,
  // Notification type which matches the scope of notify subscription
  type: string,
  // Short message used in the title of the notification
  title: string,
  // Long messages used in the body of the notification
  body: string,
  // Image URL used to display with the notification. If null, the app's icon from Notify Config is used instead
  icon: string | null,
  // Redirect URL for call-to-action related to notification. If null, there is no link
  url: string | null,
}
```

## Notify Message

```typescript
{
  // ID of the notification
  id: string,
  // Unix ms timestamp when the notification was sent
  sent_at: number,
  // Notification type which matches the scope of notify subscription
  type: string,
  // Short message used in the title of the notification
  title: string,
  // Long messages used in the body of the notification
  body: string,
  // Image URL used to display with the notification. If empty, the app's icon from Notify Config is used instead
  icon: string,
  // Redirect URL for call-to-action related to notification. If empty, there is no link
  url: string,
  // If the notification was read or not
  read: boolean,
}
```

## Notify Notification Record

```jsonc
{
  "id": string,
  "topic": string,
  "publishedAt": Int64,
  "message": NotifyNotification
}
```

## Notify Server Subscriptions

```jsonc
NotifyServerSubscription[]
```

`NotifyServerSubscription`:
```typescript
{
  appDomain: string, // App domain that the subscription refers to
  appAuthenticationKey: string, // did:key encoded ed25519 app authentication key
  symKey: string, // Symetric key used for notify topic. sha256 to get notify topic to manage the subscription and call wc_notifySubscriptionUpdate and wc_notifySubscriptionDelete
  account: Account, // CAIP-10 account
  scope: string[], // Array of notification types enabled for this subscription
  expiry: number, // Unix timestamp of expiration
}
```

## Notify Registration Params
`NotifyRegistrationParams`
```typescript
{
  payload: Cacao.Payload,
  privateIdentityKey: string,
}

```

## Notify Notification Type
`NotifyNotificationType`
```typescript
{
  "description": string, 
  "id": string,
  "enabled": boolean,
  "name": string,
  "imageUrls": {
    "sm": string,
    "md": string,
    "lg": string,
  }
}
```

