# Blockchain API Sessions and Permissions

## Sessions permissions storage

### Get permissions list for account

Used to get account list of active sessions

`GET /v1/sessions/{address}?projectId={projectId}`

* `address` - CAIP-10 address format.
* `projectId` - The project identifier.

#### Success response body:

```typescript
{
    pci: [string]
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
* `projectId` - The project identifier.

#### Success response body:

```typescript
{
    "type": string,
    "data": any,
    "required": bool,
    "onChainValidated": bool
}
```

#### Response error codes:

* `400 Bad request` - Wrong requested address format.

## Add a new permission 

Creating a new permission session for the account

`POST /v1/sessions/{address}?projectId={projectId}`

* `address` - CAIP-10 address format.
* `projectId` - The project identifier.

### Request body:

The POST request body should be in JSON format and following schema:

```typescript
{
    permissions:[
        {
          "type": string,
          "data": any,
          "required": bool,
          "onChainValidated": bool
        }
    ]
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

## Revoke permission 

Revoking a permission from account sessions.

`POST /v1/sessions/{address}/revoke/?projectId={projectId}`

* `address` - CAIP-10 address format.
* `projectId` - The project identifier.

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
