# Relay Webhooks

Webhooks is a mechanism to register HTTP watch webhooks that get triggered when a relay client ("the watched client") is involved with a published message. The conditions for these watch events can be filtered by various factors. The presence of a webhook registration does not affect the delivery of the message in any way and only observes the message flow. See the diagram below for an explanation on the specific types of watch events that can be triggered.

The relay will guarantee delivery of these watch webhooks up to the TTL of the published message. However, because HTTP services are intended to be highly available, the retry period is capped at 259,200 seconds (3 days). The relay will use exponential backoff to retry delivering watch events in case the target service is unavailable. The delivery will be retried if there is an HTTP error, or 5xx class of errors. 4xx status codes are considered successful delivery and will not be retried.

Watch events are signed by the relay's asymmetric keypair. The public key can be obtained with the `/public-key` endpoint, and is also returned in the response of the `irn_watchRegister` request. There is not currently a procedure to rotate this key and it can be assumed that it is relatively fixed.

Any relay client can register a webhook on behalf of the watched client using the [`irn_watchRegister`](./relay-server-rpc.md#register-watch-webhook) RPC request using either of the HTTP or WebSocket transport methods. The payload is a JWT signed by the watched client and includes all of the parameters for the webhook. Any client can also unregister the webhook using the [`irn_watchUnregister`](./relay-server-rpc.md#unregister-watch-webhook) RPC request which also is a JWT payload signed by the watched client. The client registering the webhook may or may not be the same as the watched client, and this depends on the specific use case.

The webhook registration is remembered by the relay for a maximum of 30 days, and must be renewed periodically or else the registration will expire and watch events will no longer be delivered. If a watched client registers a webhook with the same service URL and webhook URL, then it will replace the existing webhook. This can be used to renew the webhook registration, or change the other parameters. A watched client can register a maximum of 64 webhooks at once. Attempting to register more than 64 webhooks will return an error.

The diagram below shows the flow of a message from a publisher to subscriber, and the 4 potential watch events it could trigger.

![Webhooks message flow](/assets/webhooks.png)

`client_id` is the client ID of the watched client. Watch events are triggered when this client ID either publishes or is subscribed to receive a message. Webhook events cannot be filtered by topic, and can only be filtered by status, type, and tag.

Webhooks can be setup to trigger on one or more of these events. Available statuses are `accepted`, `queued`, and `delivered`. Available types are `publisher` and `subscriber`. Valid combinations of these statuses and types are:

- `accepted` & `publisher`: The client publishes a message, regardless if any subscribers are subscribed to the topic or will receive the message.
- `accepted` & `subscriber`: The client is subscribed to the topic, and has received the message or the message has been stored in the mailbox.
- `queued` & `subscriber`: The client is subscribed to the topic, but because it does not have a websocket connection open to receive the message immediately, the message has been stored in the mailbox instead.
- `delivered` & `subscriber`: The client received the message by being connected via websocket when the message was published, connected to the websocket and received the message from the mailbox, or called `irn_fetchMessages` or `irn_batchFetchMessages`.

If a watched client never receives the message, the message will persist in the mailbox for the TTL of the message. So if webhooks are exclusively used to deliver messages to the watched client, rather than another method (such as websocket or `irn_fetchMessages`) then it is considered a best practice to pair this with the usage of the [`irn_batchReceive`](./relay-server-rpc.md#batch-receive) RPC method to clear the mailbox of all messages so it doesn't consume resources on the relay unnecessarily.
