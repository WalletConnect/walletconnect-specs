---
description: Hedera JSON-RPC Methods
---

# Hedera

The following methods are meant to be implemented on the dApp and wallet with the [`hedera-sdk-js` library](https://github.com/hashgraph/hedera-sdk-js).

## hedera_signAndExecuteTransaction

The `hedera_signAndExecuteTransaction` method is a generic method for sending a transaction that has been converted to bytes to the wallet for signing and execution. The dApp can therefore construct any type of Hedera transaction with the [`hedera-sdk-js` library](https://github.com/hashgraph/hedera-sdk-js)

### Parameters

    1. `Object` - signAndExecuteTransaction parameters
      1.1 `transaction` : `Object` - transaction parameters
        1.1.1 `type` : `String` - The Hedera function name (*1)
        1.1.2 `bytes` : `String` - Transaction that has been converted to bytes array (*2) and encoded as a base64 string.

\*1. Full list of functions can be found [here](https://hashgraph.github.io/hedera-protobufs/#proto.HederaFunctionality), and are available via the [`RequestType` class](https://github.com/hashgraph/hedera-sdk-js/blob/50d7f76ffd1fd797a029192fab08b035720998b9/src/RequestType.js) from the Hedera SDK.

\*2. Transactions generated with the Hedera SDK can be converted to bytes with the [`toBytes` method](https://github.com/hashgraph/hedera-sdk-js/blob/50d7f76ffd1fd797a029192fab08b035720998b9/src/transaction/Transaction.js#L1015).

### Returns

    1. `Object` - Result of transaction submission to Hedera network
      1.1. `response` : `Object` - Response from Hedera network from transaction submission
        1.1.1. `nodeId` : `String` - The Hedera node the transaction was submitted to
        1.1.2. `transactionHash` : `String` - The hash of the transaction
        1.1.3. `transactionId` : `String` - Tranasction ID, which included the payer Account ID and the consensus timestamp
      1.2 `receipt` : `TransactionReceipt Object` - Details of the completed transaction (*1)

\*1. See [here](https://docs.hedera.com/hedera/sdks-and-apis/hedera-api/miscellaneous/transactionreceipt) for a detailed description of `TransactionReceipt` properties

### Example

#### Request

```json
{
  "id": 1,
  "jsonrpc": "2.0",
  "method": "hedera_signAndExecuteTransaction",
  "params": {
    "transaction": {
      "type": "CryptoTransfer",
      "bytes": "Co8BKowBCocBChoKDAjchKWmBhDW..."
    }
  }
}
```

#### Result

```json
{
  "id": 1,
  "jsonrpc": "2.0",
  "result": {
    "response": {
      "nodeId": "0.0.3",
      "transactionHash": "252b8fd...",
      "transactionId": "0.0.12345678@1689281510.675369303"
    },
    "receipt": {
      "status": {
        "_code": 22
      },
      "accountId": null
      // ...Truncated -- see note about TransactionReceipt above
    }
  }
}
```
