---
description: Hedera JSON-RPC Methods
---

# Hedera

The following JSON-RPC methods offer native integration into Hedera utilizing the [Hedera APIs](https://hashgraph.github.io/hedera-protobufs/) and the [Hedera SDKs](https://docs.hedera.com/hedera/sdks-and-apis/sdks).

Hedera documentation can be found at [docs.hedera.com](https://docs.hedera.com/hedera/). There are a number of specific resources referenced in muliple methods below.

 - The Hedera network structure is summarized by [Mainnet Nodes](https://docs.hedera.com/hedera/networks/mainnet/mainnet-nodes)
 - The full list of Hedera functionality is described by the protobuf definitions: [Hedera Functionality](https://hashgraph.github.io/hedera-protobufs/#proto.HederaFunctionality)
 - The `signerAccountId` utilized in the methods below is specified by [HIP-30](https://hips.hedera.com/hip/hip-30)
 - A Hedera Transaction ID is composed of the account id that pays for a transaction and the valid start timestamp in nanoseconds: [Hedera Transaction ID](https://docs.hedera.com/hedera/sdks-and-apis/sdks/transactions/transaction-id)
 - There are pre-processing validation response codes returned by the network: [ResponseCodeEnum](https://github.com/hashgraph/hedera-protobufs/blob/f36e05bd6bf3f572707ca9bb338f5ad6421a4241/services/response_code.proto#L32)


 _Hedera has a separate open-source project implementing parts of the [Ethereum JSON-RPC standard](https://docs.hedera.com/hedera/core-concepts/smart-contracts/json-rpc-relay) which is not covered in this documentation._

## Methods

- [`hedera_signAndExecuteTransaction`](#hedera_signandexecutetransaction)
- [`hedera_signTransaction`](#hedera_signtransaction)
- [`hedera_executeTransaction`](#hedera_executetransaction)
- [`hedera_signAndExecuteQuery`](#hedera_signandexecutequery)
- [`hedera_signMessage`](#hedera_signmessage)
- [`hedera_getNodeAddresses`](#hedera_getnodeaddresses)

## hedera\_signAndExecuteTransaction

The `hedera_signAndExecuteTransaction` method is a generic method for executing a transaction on the Hedera network. A dApp can begin by constructing a transaction with one of the Hedera SDKs or by constructing the raw protobuf messages and may select one or more consensus nodes that are authorized to execute the transaction.

The dApp then constructs a list of valid transaction bytes that differ only in the node account id and serializes the list, for example by using the `toBytes()` method of an SDK. Finally, the dApp base64 encodes the resulting bytes. This final base64 encoded string of bytes is sent as a method param titled `transactionList` to the wallet.

Wallets and SDKs must take special care to verify that each transaction in the list differs only in the node that is authorized to submit the transaction and does NOT differ in intent before submitting to an end user for approval and ultimately signing.


### Parameters

    1. `Object` - signAndExecuteTransaction parameters
      1.1 `signerAccountId` : `Object` - Hedera account id in the format `<network>:<shard>.<realm>.<num>-<optional-checksum>`
      1.2 `transactionList` : `Object` - base64 encoded string of TransactionList bytes

### Returns

    1. `Object` - Result of transaction submission to Hedera network
      1.1. `nodeId` : `String` - The Hedera node the transaction was submitted to
      1.1. `transactionHash` : `String` - The hash of the transaction
      1.1. `transactionId` : `String` - Transaction ID, which includes the payer account id and the valid start timestamp

### Error

In certain conditions, the Hedera network with return a response that signifies a pre-processing validation error, for example, when the transaction has expired. In these cases, wallets will return an error with the following format:

    1. `Object` - Result of transaction submission to Hedera network
      1.1. `code` : 9000 - the reserved Wallet Connect error code for unknown or errors not related to the Wallet Connect protocol
      1.1. `message` : `String` - A human readable string describing the nature of the failure
      1.1. `data` : `Number` - An integer representing the ResponseCodeEnum value returned from the Hedera Node, which indicates the reason for the failure


### Example

#### Request
```json
{
  "id": 1,
  "jsonrpc": "2.0",
  "method": "hedera_signAndExecuteTransaction",
  "params": {
    "signerAccountId": "hedera:testnet:0.0.12345",
    "transactionList": "Co8BKowBCocBChoKDAjchKWmBhDW..."
  }
}
```

#### Result
```json
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": {
    "nodeId": "0.0.3",
    "transactionHash": "252b8fd...",
    "transactionId": "0.0.12345678@1689281510.675369303"
  }
}
```

#### Error
```json
{
  "id": 1,
  "jsonrpc": "2.0",
  "error": {
    "code": 9000,
    "message": "The transaction failed with precheck code...",
    "data": 6
  }
}
```
## hedera\_signTransaction

The `hedera_signTransaction` signs a TransactionBody and returns a SignatureMap to the caller.

### Parameters

    1. `Object` - signTransaction parameters
      1.1 `signerAccountId` : `Object` - hedera account id in the format `<network>:<shard>.<realm>.<num>-<optional-checksum>`
      1.2 `transactionBody` : `Object` - TransactionBody 

### Returns

    1. `Object` - signTransaction parameters
      1.1 `signatureMap` : `Object` - base64 encoded SignatureMap object

### Example

#### Request

```json
{
  "id": 1,
  "jsonrpc": "2.0",
  "method": "hedera_signTransaction",
  "params": {
    "signerAccountId": "hedera:testnet:0.0.12345",
    "transactionBody": "Co8BKowBCocBChoKDAjchKWmBhDW..."
  }
}
```

#### Result

```json
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": {
    "signatureMap": "VGhpcyBpcyBqdXN0IHNvbWUgc3R1..."
  }
}
```

## hedera\_signMessage

This method accepts a plain text string value as input. If approved by the user, the controller UTF-8 encodes this message prepended with "\x19Hedera Signed Message:\n" plus the length of the message and signs the resulting bytes in the same manner as HAPI transactions are signed. The resulting signature(s) is transmitted back to the user encoded in a SignatureMap structure. The pseudo code for computing the signature is as follows:

```javascript

<Ed25519 or ECDSA Key>.sign("\x19Hedera Signed Message:\n" + len(message) + message)
```


### Parameters

    1. `Object` - signMessage parameters
      1.1 `signerAccountId` : `Object` - hedera account id in the format `<network>:<shard>.<realm>.<num>-<optional-checksum>`
      1.2 `message` : `string`

### Returns

    1. `Object` - signMessage result
      1.1 `signatureMap` : `string` - base64 encoded SignatureMap

### Example

#### Request

```json
{
  "id": 1,
  "jsonrpc": "2.0",
  "method": "hedera_signMessage",
  "params": {
    "signerAccountId": "hedera:testnet:0.0.12345"
    "message": "Co8BKowBCocBChoKDAjchKWmBhDW..."
  }
}
```

#### Result

```json
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": {
    "signatureMap": "CAAQABjMrxoYABIGCAAQABgHGIDIr..."
  }
}
```
