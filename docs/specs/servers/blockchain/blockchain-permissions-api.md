# Blockchain API Sessions and Permissions

## Sessions permissions storage

### Get permissions list

Used to get account list of active sessions

`GET /v1/sessions/{address}?projectId={projectId}`

* `address` - CAIP-10 address format.
* `projectId` - The project identifier.

#### Success response body:

```typescript
{
    sessions: []
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
  message: {
    permission: {},
    timestamp: string
  },
  signature: string,
}
```

* `message` - Message that used in signature:
    * `permission` - New permission object.
    * `timestamp` - Current unixtime timestamp. The signature is valid for 10 seconds.
* `signature` - Ethereum signature for the signed `message` to check the address ownership.

#### Success response body:

Response will contain an updated list of sessions for the address.

```typescript
{
    sessions: []
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
  message: {
    pci: string,
    timestamp: string
  },
  signature: string,
}
```

* `message` - Message that used in signature:
    * `pci` - Unique permission context identifier.
    * `timestamp` - Current unixtime timestamp. The signature is valid for 10 seconds.
* `signature` - Ethereum signature for the signed `message` to check the address ownership.

#### Success response body:

Response will contain an updated list of sessions for the address.

```typescript
{
    sessions: []
}
```

#### Response error codes:

* `400 Bad request` - Wrong format in request.
