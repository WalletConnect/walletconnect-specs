# Decrypted Notifications

The below class introduces the general way of handling decrypted notifications notifications (Sign, Auth) received by a wallet. This should be handled in the background service where the implementation details differs on each platform.

```typescript
abstract class DecryptedNotification {
    // decrypt encrypted request
    public decryptMessage(params: { topic: string, encryptedMessage: string }): Promise<Message>;

    // get notification metadata for a given request
    public getMetadata(params: { topic: string }): Promise<Metadata>;
}

type Message = {
    id: string,
    jsonrpc: string,
    method: string,
    params: string,
}

type Metadata = {
    name: string,
    description: string,
    url: string,
    icons: string[],
    url: string,
}
```

Clients will be pushed all messages sent on the topic, so the client should filter these by the message's `tag` and only display push notifications for the following methods:
- Sign: `wc_sessionPropose` (1100), `wc_sessionRequest` (1108)
- Auth: `wc_authRequest` (3000)