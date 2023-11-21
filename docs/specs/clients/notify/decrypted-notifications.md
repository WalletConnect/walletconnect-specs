# Decrypted Notifications

The below class introduces the general way of handling notify notifications received by a wallet. This should be handled in the background service where the implementation details differs on each platform.

```typescript
abstract class DecryptedNotification {
    // decrypt encrypted request
    public decryptMessage(params: { topic: string, encryptedMessage: string }): Promise<NotifyMessage>;
}

type NotifyMessage = {
    id: string,
    type: string,
    title: string,
    body: string,
    icon: string,
    url: string,
}
```

Clients will be pushed all messages sent on the topic, so the client should filter these by the message's `tag` and only display push notifications for the following methods:
- Notify: `wc_notifyMessage` (4002)