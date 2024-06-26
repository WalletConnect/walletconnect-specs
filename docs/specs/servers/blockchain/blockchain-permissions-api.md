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

Response will contain a new generated ECDSA key and PCI of the new permission.

```typescript
{
    key: string,
    pci: string
}
```

#### Response error codes:

* `400 Bad request` - Wrong format in request.

## Updating permissions context

Updating permissions context for the certain permission idenitifier.

`POST /v1/sessions/{address}/context/?projectId={projectId}`

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
          type: string,
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
* `signature` - Signature signed by the key provided during the permission creation.
* `context` - Permissions context object to update.

#### Success response body:

* `202 Accepted` - Successfully updated.

#### Response error codes:

* `400 Bad request` - Wrong format in request.

## Revoke permission 

Revoking a permission from account sessions.

`POST /v1/sessions/{address}/revoke/?projectId={projectId}`

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
* `signature` - Signature signed by the key provided during the permission creation.

#### Success response body:

* `202 Accepted` - Successfully revoked.

#### Response error codes:

* `400 Bad request` - Wrong format in request.
