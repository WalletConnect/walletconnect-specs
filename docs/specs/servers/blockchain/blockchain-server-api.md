# Blockchain API Overview

## Blockchain RPC

Blockchain RPC API for interacting with different blockchains can be found in a separate `Blockchain RPC` subsection.

## Hexless account profiles API

Email wallet users can register a profile name e.g. `coolname.connect.id` and use it instead of the `0x` address.

Server API exposes forward lookup for the `name.connect.id` -> `0x` address with chain IDs and reverse lookup for the `0x` -> `name.connect.id`.

Users can also update the name data including avatar, bio and bind addresses.

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

### Register the name

Used to register account name.

`POST /v1/profile/account/{name}`

* `name` - is the name to register e.g. `coolname.connect.id`

#### Request body:

The POST request body should be in JSON format with the folowing structure:

```typescript
{
    "message": string,
    "coin_type": string,
    "signature": string,
    "address":string,
}
```

* `message` - JSON serialized string that should contain the following fields:
    * `name` - is the name to register or update.
        * Alphabetic characters (A-Z a-z), numeric characters (0-9), the minus sign (-) and the period (.)
        * Max 255 characters length.
    * `coin_type` - Coin type by [SLIP-44](https://github.com/satoshilabs/slips/blob/master/slip-0044.md).
        Only the Ethereum `60` is now supported.
    * `chain_id` - The corresponding coin_type chain id.
    * `address` - address that bind to the account name.
        * Max 100 characters length.
    * `attributes` - (Optional) key value object of the name attributes:
        * `avatar` - (Optional) avatar url.
        * `bio` - (Optional) account profile self description.
    * `timestamp` - current unixtime timestamp. The signature is valid for 10 seconds.
* `coin_type` - Coin type by [SLIP-44](https://github.com/satoshilabs/slips/blob/master/slip-0044.md).
    Only the Ethereum `60` is now supported.
* `signature` - Ethereum signature for the signed `message` to check the address ownership.
* `address` - address that bind to the account name, should be the same as in `message.address`.
        * Max 100 characters length.

#### Success response codes:

* `200 Ok` - account name is registered.

#### Response error codes:

* `400 Bad Request` - some parameters in request body were missed or wrong.
* `401 Unauthorized` - signature verifying error.

## Generators

### Pay SDK URL

This endpoint is used to generate the On Ramp Pay SDK URL.

`POST /v1/generators/onrampurl?projectID={projectID}`

* `projectID` - is the project identifier

#### Request body:

The POST request body should be in JSON format with the following structure:

```typescript
{
    "destinationWallets": object[],
    "partnerUserId": string,
    "defaultNetwork": (optional) string,
    "presetCryptoAmount": (optional) number,
    "presetFiatAmount": (optional) number,
    "defaultExperience": (optional) "send" | "buy",
    "handlingRequestedUrls": (optional) bool
}
```

* `destinationWallets` - array of objects which define blockchain types (blockchains) and destination addresses (address):
    * `address` - destination address where the purchased tokens will be sent.
    * `blockchains` - (Optional) list of string blockchains enabled for the associated address.
    * `assets` - (Optional) list of string assets enabled for the associated address.
    * `supportedNetworks` - (Optional) list of strings with restricted networks available for the associated assets.
* `partnerUserId` - unique ID representing the client app user. Must be less than 50 chars.
* `defaultNetwork` - (Optional) default network as [CAIP10](https://github.com/ChainAgnostic/CAIPs/blob/main/CAIPs/caip-10.md) that should be selected when multiple networks are present e.g. `EIP155:1` for Ethereum.
* `presetCryptoAmount` - (Optional) preset crypto amount value.
* `presetFiatAmount` - (Optional) preset fiat amount value (for USD, CAD, GBP, EUR only). Ignored if `presetCryptoAmount` is set.
* `defaultExperience` - (Optional) default visual experience: either transfer funds from Coinbase `send` or buy assets `buy`.
* `handlingRequestedUrls` - (Optional) prevents the widget from opening URLs directly.

#### Success response body:

* `url` - generated URL for the On Ramp Pay SDK.

#### Response error codes:

* `400 Bad Request` - some parameters in request body were missed or wrong.
* `401 Unauthorized` - projectID verification error.
