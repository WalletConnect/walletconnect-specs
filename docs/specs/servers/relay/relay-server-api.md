# Relay Server API

## WebSocket

The `/` endpoint is used to establish WebSocket connections. Messages sent are JSON-RPC requests and responses are JSON-RPC responses. See [supported JSON-RPC methods](./relay-server-rpc.md) for valid JSON-RPC payloads.

### Query parameters

- `auth` (required) - A JWT for the client
- `projectId` (required) - The project ID
- `request` - A base64-encoded [JSON-RPC method](./relay-server-rpc.md) to be executed in-parallel with the connection being established. The JSON-RPC response is sent over the WebSocket connection to the client.
- `ua` - A user agent string
- `useOnCloseEvent` - unused?

## HTTP RPC

`POST /rpc` can be used to perform JSON-RPC requests. See [supported JSON-RPC methods](./relay-server-rpc.md) on what JSON-RPC methods are supported as POST bodies.
