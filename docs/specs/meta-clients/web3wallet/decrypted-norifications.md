# Decrypted Notification

The below class introduces the general way of handling decprytped notifications received by a wallet. The implementation details differs on each platform.

```typescript

abstract class DecryptedNotification {
	//decrypt encrypted request
	public decryptMessage(params: { topic: string, encryptedMessage: string }): Promise<Message>;

	//get notification metadata for a given request
	public getNotificationMetadata(params: { topic: string, message: Message }): Promise<Notification>;
}

    //Message
	{
        "id": string,
		"jsonrpc": string, 
        "method": string, 
        "params": string, //Sign(proposal, request) params, Auth request params, Notify params
	}

    //Notification
	{
        "title": string,
        "body": string,
        "icon": string,
        "url": string,
        "type": string,
    }
```