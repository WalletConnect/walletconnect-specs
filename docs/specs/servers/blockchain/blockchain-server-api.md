# Blockchain API Overview

## Blockchain RPC

Blockchain RPC API for interacting with different blockchains can be found in a separate `Blockchain RPC` subsection.

## Hexless account profiles API

Email wallet users can register a profile name e.g. `coolname.connect.id` and use it instead of the `0x` address.

Server API exposes forward lookup for the `name.connect.id` -> `0x` address with chain IDs and reverse lookup for the `0x` -> `name.connect.id`.

### Name lookup

Used to lookup address for the name.

`GET /v1/profile/account/{name}`

* `name` - is the registered name. eg. `coolname.connect.id`
    * Case insensitive alphabetic characters (a-z), numeric characters (0-9), the minus sign (-) and the period (.)
    * Max 255 characters length.

#### Success response body:

```typescript
{
    "name": string,
    "registered": number,
    "updated": number,
    "addresses": object[],
    "attributes": object[],
}
```

* `name` - registered account name.
* `registered` - unix time stamp of the name registration.
* `updated` - unix time stamp  of the last name data update.
* `addresses` - list of objects with name addresses data:
    * `blockchain` - blockchain of the address. e.g. `eip155` for the Ethereum.
    * `chain_id` - chain ID of the address. Can be NULL for the mainnet.
    * `address` - address according to the blockchain format.
    * `created` - unix time stamp when the address was added.
* `attributes` - key value object of the name attributes:
    * `avatar` - (Optional) avatar url.
    * `bio` - (Optional) account profile self description.

#### Response error codes:

* `400 Bad request` - wrong requested name format.
* `404 Not Found` - the requested name is not registered.

### Reverse name lookup

Lookup registered account name for the address in all chains:

`GET /v1/profile/reverse/{address}`

* `address` - is the address for lookup in all chains. eg. `0xab16a96D359eC26a11e2C2b3d8f8B8942d5Bfcdb`

Or the lookup for account name by the address in a specified chain:

`GET /v1/profile/reverse/{blockchain}/{address}`

* `blockchain` - the blockchain name where lookup for the registered address and name.
* The list of currenlty supported blockchains:
    * `eip155` - Ethereum mainnet
* `address` - is the address for lookup. eg. `0xab16a96D359eC26a11e2C2b3d8f8B8942d5Bfcdb`

#### Success response body:

```typescript
{
    "name": string,
    "registered": number,
    "updated": number,
    "addresses": object[],
    "attributes": object[],
}
```

* `name` - registered account name.
* `registered` - unix time stamp of the name registration.
* `updated` - unix time stamp  of the last name data update.
* `addresses` - list of objects with name addresses data:
    * `blockchain` - blockchain of the address. e.g. `eip155` for the Ethereum.
    * `chain_id` - chain ID of the address. Can be NULL for the mainnet.
    * `address` - address according to the blockchain format.
    * `created` - unix time stamp when the address was added.
* `attributes` - key value object of the name attributes:
    * `avatar` - (Optional) avatar url.
    * `bio` - (Optional) account profile self description.

#### Response error codes:

* `400 Bad Request` - wrong requested address format.
* `404 Not Found` - the requested address is not registered for any name or for the specified chain.

### Name suggestion

Used to lookup for suggestions for the new name by returning 3 unique usernames.

`GET /v1/profile/suggestion`

#### Success response body:

```jsonc
{
    "suggestions": string[],
}
```

* `suggestions` - suggested profile names.
