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
    pci: string[] // List of sessions PCIs for this account
}
```

#### Response error codes:

* `400 Bad request` - Invalid requested address format.

### Get permissions by address

Used to get permissions by address

`GET /v1/sessions/{address}?projectId={projectId}`

* `address` - CAIP-10 address format.
* `projectId` - Required. The project identifier.

#### Success response body:

The response is a list of PCIs with permissions and policy:

```typescript
{
    "pcis": [pci]
}
```

The `pci` object:

```typescript
{
    "project":
    {
      "id": string,
      "name": string,
      "url": ?string,
      "iconUrl": ?string,
    }
    "pci": string,
    "expiration": number,
    "createdAt": number,
    "permissions": [permission]
    "policies": [policy],
    "context": string
}
```

#### Response error codes:

* `400 Bad request` - Invalid requested address format or PCI not found.

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
    pci: string, // New unique permission controller identifier.
    key: string // Public (verifying) secp256k1 key for the generated PCI key in sec1/der format encoded by base64.
}
```

#### Response error codes:

* `400 Bad request` - Invalid parameter format in request.

## Updating permissions context

Updating permissions context for the certain permission idenitifier.

`POST /v1/sessions/{address}/context?projectId={projectId}`

* `address` - CAIP-10 address format.
* `projectId` - Required. The project identifier.

### Request body:

The POST request body should be in JSON format and following schema based on the [ERC-7715](https://github.com/ethereum/ERCs/blob/a75e2d80698441f5da9e0fe98d38122a862aed89/ERCS/erc-7715.md#signers):

```typescript
{
    pci: string, // PCI to update
    signature: string, // Signature of canonicalized JSON `context` object signed by the key provided during the permission creation. The signature must be provided as DER, SEC1 and encoded in Base64 format.
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

#### Success response body:

* `200 Ok` - Successfully updated.

#### Response error codes:

* `400 Bad request` - Invalid parameter format in request.
* `401 Unauthorized` - Invalid signature.

## Revoke permission 

Revoking a permission from account sessions.

`POST /v1/sessions/{address}/revoke?projectId={projectId}`

* `address` - CAIP-10 address format.
* `projectId` - Required. The project identifier.

### Request body:

The POST request body should be in JSON format and following schema:

```typescript
{
    pci: string, // PCI to revoke.
    signature: string, // Signature of signed `pci` field by the key provided during the permission creation. The signature must be provided as DER, SEC1 and encoded in Base64 format.
}
```

#### Success response body:

* `200 Ok` - Successfully revoked.

#### Response error codes:

* `400 Bad request` - Invalid parameter format in request.
* `401 Unauthorized` - Invalid signature.
