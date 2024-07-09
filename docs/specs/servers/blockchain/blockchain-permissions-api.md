# Blockchain API Sessions and Permissions

This API is **unstable**, not yet production ready and can be changed at any time.

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

The POST request body should be in JSON format and following schema:

```typescript
{
    pci: string,
    signature: string,
    context: {
      {
        signer: {
          permissionType: string,
          ids: [string]
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
	pci: String, // Permission controller identifier.
	userOp: // ERC-4337 pseudo transaction object:
  {
    sender: string, // The address of the smart contract account.
    nonce: number, // Anti-replay protection; also used as the salt for first-time account creation.
    initCode: string, // Code used to deploy the account if not yet on-chain.
    callData: string, // Data that's passed to the sender for execution.
    callGasLimit: number, // Gas limit for execution phase.
    verificationGasLimit: number, // Gas limit for verification phase.
    preVerificationGas: number, // Gas to compensate the bundler.
    maxFeePerGas: number, // Maximum fee per gas (similar to EIP-1559(opens in a new tab) max_fee_per_gas).
    maxPriorityFeePerGas: number, // Maximum priority fee per gas (similar to EIP-1559 max_priority_fee_per_gas).
    paymasterAndData: string, // Paymaster Contract address and any extra data required for verification and execution (empty for self-sponsored transaction).
    signature: string // Signature of all `userOp` fields hashed by keccak256 and signed by the key provided during the permission creation.
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

* `400 Bad request` - Wrong format in request.
* `401 Unauthorized` - Wrong signature.
