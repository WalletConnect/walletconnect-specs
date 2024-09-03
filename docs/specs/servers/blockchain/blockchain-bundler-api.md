# Blockchain API Bundlers interaction

This API provides a bundlers JSON-RPC interaction. 
The supported `userOperation` interaction schema is according to v0.7 ERC-4337 specification.

## Make a bundler JSON-RPC request

Used to make a JSON-RPC call to the designated bundler.

`POST /v1/bundler?projectId={projectId}&chainId={chainId}&bundler={bundlerName}`

* `projectId` - Required. The project identifier.
* `chainId` - CAIP-2 chain identifier.
* `bundlerName` - Bundler name to use.

The list of currently supported bundler names: 
* `pimlico` - [Pimlico bundler](https://docs.pimlico.io/permissionless/reference/bundler-actions/sendUserOperation).

#### Request body:

```typescript
{
    id:  number, // Unique identifier. Default is 1.
    jsonrpc: string, // JSON-RPC version. Default 2.0
    method: string, // ERC-4337 bundler operation methods. Please check supported methods below.
    params: string[], // Array of string variables input for the method.
}
```

List of supported `method` values:
* `wallet_getCallsStatus` - Returns the status of a call batch that was sent via `wallet_sendCalls`.
* `wallet_showCallsStatus` - Requests that a wallet shows information about a given call bundle that was sent with `wallet_sendCalls`.
* `eth_sendUserOperation` - Send the user operation to the bundler. The v0.7 user operation object is expected.
* `eth_getUserOperationReceipt` - Get the receipt for the UserOperation.
* `eth_estimateUserOperationGas` - Simulates the user operation and estimates the appropriate gas limits for it.
* `pm_sponsorUserOperation` - Endpoint for verifying paymasters.

#### Success response body:

Successfull response body will contain the following JSON-RPC structure:

```typescript
{
    id: number, // Unique identifier passed to the operation. Default is 1.
    jsonrpc: string, // JSON-RPC version. Default 2.0
    result: any // Result object from the operation.
}
```

#### Response error codes:

* `400 Bad request` - Wrong input variables format.
