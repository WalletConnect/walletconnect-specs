# Blockchain API Bundlers interaction

This API provides a bundlers JSON-RPC interaction. 
The supported `userOperation` interaction schema is according to v0.7 ERC-4337 specification.

## Make a bundler JSON-RPC request

Used to make a JSON-RPC call to the designated bundler.

`POST /v1/bundler?projectId={projectId}&chainId={chainId}&bundler={bundlerName}`

* `projectId` - Required. The project identifier.
* `chainId` - CAIP-2 chain identifier.
* `bundlerName` - (Optional) Bundler name to use.

The list of currently supported bundler names: 
* `pimlico` - [Pimlico bundler](https://docs.pimlico.io/permissionless/reference/bundler-actions/sendUserOperation).

#### Success response body:

```typescript
{
    id:  number, // Unique identifier. Default is 1.
    jsonrpc: string, // JSON-RPC version. Default 2.0
    method: string, // ERC-4337 bundler operation methods. Please check supported methods below.
    params: string[], // Array of string variables input for the method.
}
```

List of supported `method` values:
* `sendUserOperation` - Sends a user operation to the given EVM network.
* `estimateUserOperationGas` - Estimate the gas limits for a User Operation.
* `getUserOperationByHash` - Return a User Operation based on a User Operation hash.
* `getUserOperationReceipt` - Return a User Operation receipt based on a User Operation hash.
* `supportedEntryPoints` - Return the Entry Points supported by the bundler.

#### Success response body:

Successfull response body will contain the following JSON-RPC structure:

```typescript
{
    id: number, // Unique identifier passed to the operation. Default is 1.
    jsonrpc: string, // JSON-RPC version. Default 2.0
    result: object // Result object from the operation.
}
```

#### Response error codes:

* `400 Bad request` - Wrong input variables format.
