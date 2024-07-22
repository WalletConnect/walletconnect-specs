# Blockchain API Sessions and Permissions

## Sessions permissions storage

### Get permissions list for account

Used to get account list of active sessions

`GET /v1/sessions/{address}?projectId={projectId}`

* `address` - CAIP-10 address format.
* `projectId` - Required. The project identifier.

#### Success response body:

```typescript
{
    pci: string[]
}
```

* `pci` - List of sessions PCIs for this account.

#### Response error codes:

* `400 Bad request` - Wrong requested address format.

### Get permission by PCI

Used to get permission by PCI

`GET /v1/sessions/{address}/{pci}?projectId={projectId}`

* `address` - CAIP-10 address format.
* `pci` - Permission identifier.
* `projectId` - Required. The project identifier.

#### Success response body:

```typescript
{
    "permissionType": string,
    "data": string,
    "required": boolean,
    "onChainValidated": boolean
}
```

#### Response error codes:

* `400 Bad request` - Wrong requested address format or PCI not found.

## Add a new permission 

Creating a new permission session for the account

`POST /v1/sessions/{address}?projectId={projectId}`

* `address` - CAIP-10 address format.
* `projectId` - Required. The project identifier.

### Request body:

The POST request body should be in JSON format and following schema:

```typescript
{
    permission:
        {
          "permissionType": string,
          "data": string,
          "required": boolean,
          "onChainValidated": boolean
        }
}
```

#### Success response body:

Response will contain a new generated key and PCI of the new permission.

```typescript
{
    pci: string,
    key: string
}
```

* `pci` - New unique permission controller identifier.
* `key` - Generated signing (private) ECDSA P256 key in DER, SEC1 format encoded by Base64.

#### Response error codes:

* `400 Bad request` - Wrong format in request.

## Updating permissions context

Updating permissions context for the certain permission idenitifier.

`POST /v1/sessions/{address}/context?projectId={projectId}`

* `address` - CAIP-10 address format.
* `projectId` - Required. The project identifier.

### Request body:

The POST request body should be in JSON format and following schema based on the [ERC-7715](https://github.com/ethereum/ERCs/blob/a75e2d80698441f5da9e0fe98d38122a862aed89/ERCS/erc-7715.md#signers):

```typescript
{
    pci: string,
    signature: string,
    context: {
      signer: {
        type: string,
        data:{
          ids: string[],
        }
      },
      expiry: number,
      signerData: {
        userOpBuilder: string
      },
      factory: string,
      factoryData: string,
      permissionsContext: string
    }
}
```

* `pci` - PCI to revoke.
* `signature` - Signature of canonicalized JSON `context` object signed by the key provided during the permission creation. The signature must be provided as DER, SEC1 and encoded in Base64 format.
* `context` - Permissions context object to update.

#### Success response body:

* `200 Ok` - Successfully updated.

#### Response error codes:

* `400 Bad request` - Wrong format in request.
* `401 Unauthorized` - Wrong signature.

## Revoke permission 

Revoking a permission from account sessions.

`POST /v1/sessions/{address}/revoke?projectId={projectId}`

* `address` - CAIP-10 address format.
* `projectId` - Required. The project identifier.

### Request body:

The POST request body should be in JSON format and following schema:

```typescript
{
    pci: string,
    signature: string,
}
```

* `pci` - PCI to revoke.
* `signature` - Signature of signed `pci` field by the key provided during the permission creation. The signature must be provided as DER, SEC1 and encoded in Base64 format.

#### Success response body:

* `200 Ok` - Successfully revoked.

#### Response error codes:

* `400 Bad request` - Wrong format in request.
* `401 Unauthorized` - Wrong signature.

## Co-signing

Co-signing request

`POST /v1/sessions/{address}/sign?projectId={projectId}`

* `address` - CAIP-10 address format.
* `projectId` - Required. The project identifier.

### Request body:

The POST request body should be in JSON format and following schema:

```typescript
{
	pci: string, // Permission controller identifier.
	userOp: // ERC-4337 pseudo transaction object for v0.7:
  {
    sender: string, // The address of the smart contract account.
    nonce: number, // Anti-replay protection; also used as the salt for first-time account creation.
    callData: string, // Data that's passed to the sender for execution.
    callGasLimit: number, // Gas limit for execution phase.
    verificationGasLimit: number, // Gas limit for verification phase.
    preVerificationGas: number, // Gas to compensate the bundler.
    maxPriorityFeePerGas: number, // Maximum priority fee per gas (similar to EIP-1559 max_priority_fee_per_gas).
    maxFeePerGas: number, // Maximum fee per gas (similar to EIP-1559(opens in a new tab) max_fee_per_gas).
    signature: string, // Signature of all `userOp` fields hashed by keccak256 and signed by the key provided during the permission creation.
    factory?: string, // Factory address if deploying a new sender contract.
    factoryData?: string, // Data call for the factory.
    paymaster?: string, // Paymaster address
    paymasterVerificationGasLimit?: number, // Paymaster gas limits for the verification
    paymasterPostOpGasLimit?: number, // Paymaster operation gas limit
    paymasterData?: string // Bytes calldata for paymaster
  }
}
```

#### Success response body:

Response will contain user operation receipt.

```typescript
{
    userOpReceipt: string // User operation receipt data.
}
```

#### Response error codes:

* `400 Bad request` - Invalid format in request.
* `401 Unauthorized` - Invalid signature.
