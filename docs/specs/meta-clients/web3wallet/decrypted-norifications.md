# Decrypted Notification

The below class introduces the general way of handling decrypted notifications received by a wallet. The implementation details differs on each platform.

```typescript
abstract class DecryptedNotification {
    // decrypt encrypted request
    public decryptMessage(params: { topic: string, encryptedMessage: string }): Promise<Message>;

    // get notification metadata for a given request
    public getNotificationMetadata(params: { topic: string, message: Message }): Promise<Notification>;
}

type Message = {
    id: string,
    jsonrpc: string, 
    method: string, 
    params: string,

type Notification = {
    title: string,
    body: string,
    icon: string,
    url: string,
    type: string,
}
```

Clients can expect receiving the following tags:
- Sign: `SESSION_PROPOSE(1100)`, `SESSION_REQUEST(1108)`
- Auth: `AUTH_REQUEST(3000)`
- Notify: `NOTIFY_MESSAGE(4002)`