# Blockchain API

## Identity name lookups

### Name lookup

Used to lookup name and avatar for the provided address.

`GET /v1/identity/{address}`

* `address` - The address to lookup for the name and avatar.

#### Query parameters:

* `projectId` - The project identifier.
* `chainId` - (Optional) CAIP-2 chain identifier to use for the address. 
If it's not set the Ethereum mainnet and ENS resolution is used.

#### Success response body:

```typescript
{
    "name": string,
    "avatar": string
}
```

* `name` - Registered address for the name.
* `avatar` - URI of the avatar of presented.

#### Response error codes:

* `400 Bad request` - Wrong requested address format or unsupported chain ID.

## Hexless account profiles API

Email wallet users can register a profile name e.g. `coolname.connect.id` and use it instead of the `0x` address.

Server API exposes forward lookup for the `name.connect.id` -> `0x` address with chain IDs and reverse lookup for the `0x` -> `name.connect.id`.

Users can also update the name data including avatar, bio and bind addresses.

### Name lookup

Used to lookup address for the name.

`GET /v1/profile/account/{name}`

* `name` - The registered name. eg. `coolname.connect.id`
    * Case insensitive alphabetic characters (a-z), numeric characters (0-9), the minus sign (-) and the period (.).
    * Max 255 characters length.

#### Query parameters:

* `apiVersion` - (Optional) API version specification. `1` by default. Can be `2` to force using the updated API response.

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

* `name` - Registered account name.
* `registered` - Unix time stamp of the name registration.
* `updated` - Unix time stamp  of the last name data update.
* `addresses` - Registered addresses for the name:
    * `coin type` - Map where the key is the [ENSIP-11](https://alpha-docs.ens.domains/ensip/11) coin type:
        * `address` - Address according to the blockchain format.
        * `created` - Unix time stamp when the address was added.
* `attributes` - Key value object of the name attributes:
    * `avatar` - (Optional) avatar url.
    * `bio` - (Optional) account profile self description.

#### Name was not found response:

By default, or using `apiVersion=1` query parameter the response will be `HTTP 404`.
When using `apiVersion=2` query parameter the response will be an empty array `[]`.

#### Response error codes:

* `400 Bad request` - Wrong requested name format.
* `404 Not Found` - Default or when using `apiVersion=1` query parameter: the requested name is not registered.

### Reverse name lookup

Lookup registered account name for the address in all chains:

`GET /v1/profile/reverse/{address}`

* `address` - The address for lookup in all chains. eg. `0xab16a96D359eC26a11e2C2b3d8f8B8942d5Bfcdb`

Or the lookup for account name by the address in a specified chain:

`GET /v1/profile/reverse/{coin_type}/{address}`

* `coin_type` - [ENSIP-11](https://alpha-docs.ens.domains/ensip/11) coin type for the registered address and name.
* `address` - Ihe address for lookup. eg. `0xab16a96D359eC26a11e2C2b3d8f8B8942d5Bfcdb`

#### Query parameters:

* `apiVersion` - (Optional) API version specification. `1` by default. Can be `2` to force using the updated API response.

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

* `name` - Registered account name.
* `registered` - Unix time stamp of the name registration.
* `updated` - Unix time stamp  of the last name data update.
* `addresses` - Registered addresses for the name:
    * `coin_type` - Map where the key is the [ENSIP-11](https://alpha-docs.ens.domains/ensip/11) coin type:
        * `address` - Address according to the blockchain format.
        * `created` - Unix time stamp when the address was added.
* `attributes` - Key value object of the name attributes:
    * `avatar` - (Optional) avatar url.
    * `bio` - (Optional) account profile self description.

#### Name was not found response:

By default, or using `apiVersion=1` query parameter the response will be `HTTP 404`.
When using `apiVersion=2` query parameter the response will be an empty array `[]`.

#### Response error codes:

* `400 Bad Request` - Wrong requested address format.
* `404 Not Found` - Default or when using `apiVersion=1` query parameter: the requested address is not registered for any name or for the specified chain.

### Name suggestion

Used to lookup for suggestions for the new name by returning 3 unique usernames.

`GET /v1/profile/suggestions/:name`

#### Path parameters

* `name` - The preffered name. Minimum 3 characters length.


#### Query parameters:

* `zone` - (Optional) Name zone for suggestions.

#### Success response body:

```jsonc
{
    "suggestions": object[],
}
```

* `suggestions` - List of suggestions object. The default number of suggestions is up to 5:
  * `name` - Suggested name.
  * `registered` - Boolean parameter that reflects is the name is already registered. By default only not registered names are returned, but this is usefull for the exact name match that is also included in the response list.

### Register the name

Used to register account name.

`POST /v1/profile/account`

#### Request body:

The POST request body should be in JSON format with the following structure:

```typescript
{
    "message": string,
    "coin_type": string,
    "signature": string,
    "address":string,
}
```

* `message` - JSON serialized string that should contain the following fields:
    * `name` - The name to register e.g. `coolname.connect.id`.
        * Alphabetic characters (A-Z a-z), numeric characters (0-9), the minus sign (-) and the period (.)
        * 255 characters length limit.
    * `attributes` - (Optional) key value object of the name attributes:
        * `avatar` - (Optional) avatar url.
        * `bio` - (Optional) account profile self description.
    * `timestamp` - Current unixtime timestamp. The signature is valid for 10 seconds.
* `coin_type` - Coin type according to [ENSIP-11](https://docs.ens.domains/ens-improvement-proposals/ensip-11-evmchain-address-resolution) format. If not mainnet cointype `60` is provided two address records will be added: for the provided cointype and for the mainnet.
* `signature` - Ethereum signature for the signed `message` to check the address ownership.
* `address` - Address that bind to the account name.
    * 100 characters length limit.

#### Success response codes:

* `200 Ok` - Account name is successfully registered.

#### Response error codes:

* `400 Bad Request` - Some parameters in request body were missed or wrong.
* `401 Unauthorized` - Signature verifying error.

### Updating name attributes

Used to update name attributes.

`POST /v1/profile/account/{name}/attributes`

* `name` - The registered name. eg. `coolname.wc.ink`

#### Request body:

The POST request body should be in JSON format with the following structure:

```typescript
{
    "message": string,
    "coin_type": string,
    "signature": string,
    "address":string,
}
```

* `message` - JSON serialized string that should contain the following fields:
    * `attributes` - Key value object of the name attributes:
        * `avatar` - (Optional) avatar url.
        * `bio` - (Optional) account profile self description.
    * `timestamp` - Current unixtime timestamp. The signature is valid for 10 seconds.
* `coin_type` - Coin type according to [ENSIP-11](https://docs.ens.domains/ens-improvement-proposals/ensip-11-evmchain-address-resolution) format.
* `signature` - Ethereum signature for the signed `message` to check the address ownership.
* `address` - Ownership address of the name.

#### Success response codes:

* `200 Ok` - Name attributes are successfully updated.

#### Response error codes:

* `400 Bad Request` - Some parameters in request body were missed or wrong.
* `401 Unauthorized` - Signature verification or name address ownership error.

### Updating name addresses

Used to update or add name addresses.

`POST /v1/profile/account/{name}/address`

* `name` - The registered name. eg. `coolname.wc.ink`

#### Request body:

The POST request body should be in JSON format with the following structure:

```typescript
{
    "message": string,
    "coin_type": string,
    "signature": string,
    "address":string,
}
```

* `message` - JSON serialized string that should contain the following fields:
    * `coin_type` - Coin type of the new address according to [ENSIP-11](https://docs.ens.domains/ens-improvement-proposals/ensip-11-evmchain-address-resolution) format.
    * `address` - New address to bind to the account name for the `coin_type`.
    * `timestamp` - Current unixtime timestamp. The signature is valid for 10 seconds.
* `coin_type` - Coin type according to [ENSIP-11](https://docs.ens.domains/ens-improvement-proposals/ensip-11-evmchain-address-resolution) format.
* `signature` - Ethereum signature for the signed `message` to check the address ownership.
* `address` - Ownership address of the name.

According to the [ENSIP-11](https://docs.ens.domains/ens-improvement-proposals/ensip-11-evmchain-address-resolution)
only one address per `coin_type` is supported. If an address is already assigned for the `coin_type` that address will be updated.

#### Success response codes:

* `200 Ok` - Name addresses are successfully updated.

#### Response error codes:

* `400 Bad Request` - Some parameters in request body were missed or wrong.
* `401 Unauthorized` - Signature verification or name address ownership error.

## Generators

### Pay SDK URL

This endpoint is used to generate the On Ramp Pay SDK URL.

`POST /v1/generators/onrampurl?projectID={projectID}`

* `projectId` - The project identifier

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

* `destinationWallets` - Array of objects which define blockchain types (blockchains) and destination addresses (address):
    * `address` - Destination address where the purchased tokens will be sent.
    * `blockchains` - (Optional) list of string blockchains enabled for the associated address.
    * `assets` - (Optional) list of string assets enabled for the associated address.
    * `supportedNetworks` - (Optional) list of strings with restricted networks available for the associated assets.
* `partnerUserId` - Unique ID representing the client app user. Must be less than 50 chars.
* `defaultNetwork` - (Optional) default network as [CAIP-2](https://github.com/ChainAgnostic/CAIPs/blob/main/CAIPs/caip-2.md) that should be selected when multiple networks are present e.g. `EIP155:1` for Ethereum.
* `presetCryptoAmount` - (Optional) preset crypto amount value.
* `presetFiatAmount` - (Optional) preset fiat amount value (for USD, CAD, GBP, EUR only). Ignored if `presetCryptoAmount` is set.
* `defaultExperience` - (Optional) default visual experience: either transfer funds from Coinbase `send` or buy assets `buy`.
* `handlingRequestedUrls` - (Optional) prevents the widget from opening URLs directly.

#### Success response body:

* `url` - Generated URL for the On Ramp Pay SDK.

#### Response error codes:

* `400 Bad Request` - Some parameters in request body were missed or wrong.
* `401 Unauthorized` - ProjectID verification error.

## Account 

### Balance

Used to lookup fungible assets balances

`GET /v1/account/{address}/balance`

#### Headers

* `x-sdk-version` - The AppKit release version must be provided in a request headers.
Otherwise the response will be an empty array.

#### Path parameters

* `address` - The address for lookup. eg. `0xab16a96D359eC26a11e2C2b3d8f8B8942d5Bfcdb`

#### Request arguments:

```typescript
{
    "projectId": string,
    "currency": string,
    "chain"?: string,
    "forceUpdate"?: string
}
```

* `projectId` - The project identifier.
* `currency` - Currency to calculate the assets value. e.g. `usd`.
    * Supported currencies: `btc`, `eth`, `usd`, `eur`, `gbp`, `aud`, `cad`, `inr`, `jpy`.
* `chainId` - (Optional) Filter assets by CAIP-2 chain id. Supported chain IDs:
    * `eip155:1` - Ethereum
    * `eip155:5` - Goerli
    * `eip155:56` - BinanceSmartChain
    * `eip155:100` - GnosisChain
    * `eip155:250` - Fantom
    * `eip155:328` - ZkSyncEra
    * `eip155:137` - Polygon
    * `eip155:10` - Optimism
    * `eip155:8453` - Base
    * `eip155:42220` - Celo
    * `eip155:42161` - Arbitrum
    * `eip155:43114` - Avalanche
    * `eip155:59160` - Linea
    * `eip155:81032` - Blast
    * `eip155:8508132` - Scroll
    * `eip155:7854577` - Zora
    * `eip155:1313161554` - Aurora
* `forceUpdate` - (Optional) List of comma separated token contract addresses (CAIP-10 format) to force the latest balance update on. `chainId` must be also provided for this type of call.

#### Success response body:

* `balances` - List of address balances with the following attributes:
    * `name` - Asset name. e.g. `Ethereum`
    * `symbol` - Native asset or ERC-20 symbol. e.g. `ETH`
    * `chainId` - CAIP-2 chain id. e.g. `eip155:1`
    * `address` - (Optional) token contract address. The address is null for the native token.
    * `value` - (Optional) asset value in currency calculated from the request argument.
    * `price` - Price of a single unit of the asset in currency calculated from the request argument.
    * `quantity` - Asset quantity object:
        * `decimals` - Decimal number precision of the quantity.
        * `numeric` - Numeric representation of the asset quantity.
    * `iconUrl` - URL of the asset icon.

#### Response error codes:

* `202 Accepted` - The data for the address is not fulfilled yet, re-trying is needed in up to 180 seconds.
* `400 Bad request` - Wrong requested arguments format.

### Transactions history

Used to lookup transactions list for an address.

`GET /v1/account/{address}/history`

#### Path parameters

* `address` - The address for lookup. eg. `0xab16a96D359eC26a11e2C2b3d8f8B8942d5Bfcdb`

#### Query parameters:

```typescript
{
    projectId: string,
    currency?: string,
    cursor?: string,
    onramp?: string
}
```

* `projectId` - Unique project ID.
* `currency` - (Optional) Currency applied to prices. List of available currencies: `BTC, ETH, USD, EUR, GBP, AUD, CAD, INR,JPY`.
* `cursor` - (Optional) Cursor for the next page of (100) results from the `next` response field.
* `onramp` - (Optional) Onramp history from the onramp provider. List of available providers: `coinbase`.

#### Success response body object:

* `data` - List of transactions with the following attributes:
    * `id` - Unique transaction identifier.
    * `metadata` - Transaction metadata.
        * `operationType` - Operation type.
        * `hash` - Trnsaction hash.
        * `minedAt` - Unixtime of when the transaction was minted.
        * `sentFrom` - Sender address.
        * `sentTo` - Receiver address.
        * `status` - Status of the transaction.
        * `nonce` - Transaction nonce.
        * `application` - Application object:
            * `name` - Name of the item.
            * `iconUrl` - Icon representation of the item.
        * `chain` - Chain ID.
    * `transfers` - List of transfers inside of the transaction.
        * `fungible_info` - Transaction fungible info onject.
        * `nft_info` - Object of the NFT item if present.
        * `direction` - The direction of the transaction.
        * `quantity` - Quantity if the item was sent.
        * `value` - Value of the item in requested `currency`.
        * `price` - Price of the item in requested `currency`.
* `next` - Cursor for the next page of (100) results.

#### Response error codes:

* `400 Bad request` - Wrong requested arguments format.

## Fungibles

### Price

Lookup for the current price of the fungible assset item:

`POST /v1/fungible/price`

#### Request body:

The POST request body should be in JSON format with the folowing structure:

* `projectId` - The project identifier.
* `currency` - Currency for the price calculation e.g. `usd`.
    * Supported currencies: `usd`, `eur`, `gbp`, `aud`, `cad`, `inr`, `jpy`, `btc`, `eth`.
* `addresses` - Asset's contract addresses array of strings in [CAIP-10](https://github.com/ChainAgnostic/CAIPs/blob/main/CAIPs/caip-10.md) format.

#### Success response body:

```typescript
{
    "fungibles": object[],
}
```

* `fungibles` - List of objects with fungibles on the requested address:
    * `address` - Asset implementation address.
    * `name` - Asset name. e.g. `Ethereum`.
    * `symbol` - Native asset or ERC-20 symbol. e.g. `ETH`.
    * `iconUrl` - URL of the asset icon.
    * `price` - Price of a single unit of the asset in currency calculated from the request argument.
    * `decimals` - Asset decimals.

#### Response error codes:

* `400 Bad Request` - Required argument is missed in the request.
* `422 Unprocessable Entity` - Wrong argument(s) format was passed to the request.

## Conversion

Endpoints to convert tokens.

### List of tokens available for conversion

`GET /v1/convert/tokens`

#### Path parameters

* `projectId` - The project identifier
* `chainId` - Filter by chain. CAIP-2 format. e.g. `eip155:1`

#### Success response body:

* `tokens` - List of objects which define available tokens:
    * `name` - Token name.
    * `symbol` - Token symbol.
    * `address` - Contract address of the token in CAIP-10 format.
    * `decimals` - Number of decimals for amount supported by a given token.
    * `logoUri` - URL of the token icon.
    * `eip2612` - (Optional for ERC-20 tokens) value is `true` if the token supports [eip-2612](https://eips.ethereum.org/EIPS/eip-2612)

#### Response error codes:

* `400 Bad Request` - Some parameters in request body were missed or wrong.
* `401 Unauthorized` - Project ID verification error.

### Requesting conversion quotes

`GET /v1/convert/quotes`

#### Path parameters

* `projectId` - The project identifier.
* `amount` - Amount of tokens to be converted related to the decimals of the token. Numeric value up to 18 decimals.
* `userAddress` - Caller address.
* `from` - Source CAIP-10 asset address.
* `to` - Destination CAIP-10 asset address.
* `gasPrice` - (Optional) Network price per gas in wei. By default fast network gas price.

#### Success response body:

* `quotes` - List of objects which define available conversion quotes:
    * `id` - (Optional for cross chain swaps) quote ID to be used for convert request.
    * `fromAmount` - Amount of token being sent on sending chain.
    * `fromAccount` - Source CAIP-10 account ID.
    * `toAmount` - Amount of token to be received on the destination chain.
    * `toAccount` - Destination CAIP-10 account ID.

#### Response error codes:

* `400 Bad Request` - Some parameters in request body were missed or wrong.
* `401 Unauthorized` - Project ID verification error.

### Requesting approve calldata

Generate approve calldata to allow a transaction to perform a conversion.

This step should be skipped if the token supports [eip-2612](https://eips.ethereum.org/EIPS/eip-2612).
In this case, the `permit` parameter in `Requesting calldata to convert` should be used to provide the approval signature.

`GET /v1/convert/build-approve`

####  Path parameters

* `projectId` - The project identifier.
* `userAddress` - Caller address.
* `from` - Source CAIP-10 asset address.
* `to` - Destination CAIP-10 asset address.
* `amount` - (Optional) Amount of tokens to be converted related to the decimals of the token. If not specified an infinite amount of tokens will be allowed to spend.

#### Success response body:

* `tx` - Transaction object
    * `from` - (Optional) Source.
    * `to` - Destination.
    * `data` - Transaction data.
    * `value` - Amount of tokens to be converted, denominated in the decimal precision of the token.
    * `eip155` - (Optional) For EVM conversions only.
        * `gasPrice` - Current gas price.

#### Response error codes:

* `400 Bad Request` - Some parameters in request body were missing or wrong.
* `401 Unauthorized` - Project ID verification error.

### Requesting calldata to convert

Generate calldata for convert transaction.

`POST /v1/convert/build-transaction`

####  Request body

The `POST` request body should be in JSON format with the following structure:

* `projectId` - The project identifier.
* `userAddress` - Caller address.
* `from` - Asset source CAIP-10 address.
* `to` - Asset destination CAIP-10 address.
* `amount` - Amount of tokens to be converted denominated in the decimal precision of the token.
* `eip155`- (Optional) For EVM converts only
    * `slippage` - Slippage integer value. Max. 50
    * `permit` - (Optional) [EIP-2612](https://eips.ethereum.org/EIPS/eip-2612) gasless approvals.

#### Success response body:

* `tx` - Transaction object
    * `from` - Source address (Sender).
    * `to` - Destination address (Smart contract to swap).
    * `data` - Transaction data.
    * `amount` - Amount of tokens to be received denominated in the decimal precision of the token.
    * `eip155` - (Optional) For EVM converts only
        * `gas` - Gas amount to be used.
        * `gasPrice` - Current gas price.

#### Response error codes:

* `400 Bad Request` - Some parameters in request body were missing or wrong.
* `401 Unauthorized` - Project ID verification error.

### Gas price

`GET /v1/convert/gas-price`

#### Path parameters

* `projectId` - The project identifier
* `chainId` - Chain ID in CAIP-2 format. e.g. `eip155:1`

#### Success response body:

* `standard` - Standard gas price.
* `fast` - Fast gas price.
* `instant` - Instant gas price.

### Conversion allowance

`GET /v1/convert/allowance`

#### Path parameters

* `projectId` - The project identifier.
* `tokenAddress` - Token address in CAIP-10 format. e.g. `eip155:1:0x...`.
* `userAddress` - User wallet address in CAIP-10 format. e.g. `eip155:1:0x...`.

#### Success response body:

* `allowance` - Conversion allowance amount.

#### Response error codes:

* `400 Bad Request` - Some parameters in request body were missed or wrong.
* `401 Unauthorized` - Project ID verification error.
