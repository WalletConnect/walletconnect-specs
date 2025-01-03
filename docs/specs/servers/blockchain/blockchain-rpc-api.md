# Blockchain API JSON-RPC

Blockchain RPC API for interacting with different blockchains.
You can find the currently supported chains list in [SUPPORTED CHAINS](https://github.com/WalletConnect/blockchain-api/blob/master/SUPPORTED_CHAINS.md).

## Supported chains list

This endpoint returns the list of currently supported chains list in JSON format.

`GET /v1/supported-chains`

### Response format:

The response in JSON format contains following objects:

* `http` - List of supported chains in CAIP-2 format for HTTP requests.
* `ws` - List of supported chains in CAIP-2 format for WebSocket connections.

## JSON-RPC call

Calling JSON-RPC operations on the blockchain network.

`POST /v1?chainId={chainId}&projectId={projectId}`

* `chainId` - CAIP-2 chain identifier.
* `projectId` - The project identifier.

### Request body:

The POST request body should be in JSON-RPC 2.0 specification and blockchain-specific conventions format.

For example to check the address balance:

```json
{
  "jsonrpc":"2.0",
  "method":"eth_getBalance",
  "params":["0xAff392551773CCb2574fAE23195CC3aFDBe98d18","latest"],
  "id":1
}
```
