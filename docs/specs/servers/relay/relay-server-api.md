# Relay Server API

## WebSocket

The `/` endpoint is used to establish WebSocket connections. Messages sent are JSON-RPC requests and responses are JSON-RPC responses. See [supported JSON-RPC methods](./relay-server-rpc.md) for valid JSON-RPC payloads.

### Query parameters

- `auth` (required) - a JWT for the client
- `projectId` (required) - the project ID
- `request` - a base64-encoded [JSON-RPC method](./relay-server-rpc.md)
- `ua` - a user agent string
- `useOnCloseEvent` - unused?

## HTTP RPC

`POST /rpc` can be used to perform JSON-RPC requests. See [supported JSON-RPC methods](./relay-server-rpc.md) on what JSON-RPC methods are supported as POST bodies.
