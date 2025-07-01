# API (v2)

> For the deprecated v1 specification, see [here](./api-v1-legacy.md).

## Query Parameters

All API requests now use query parameters instead of custom headers for SDK metadata, making integration simpler and more compatible with common HTTP clients and browser caching mechanisms.

### Mandatory Query Parameters

All API endpoints expect the following mandatory query parameters:

- `st=`: SDK type
  - Options: `w3m | wcm`
  - Example: `st=w3m`
- `sf=`: SDK framework
  - Options: `html | react | vue | react-native | flutter | swift | kotlin | unity`
  - Example: `sf=react`
- `sa=`: SDK adapter
  - Options: `evm-wagmi | evm-ethers | evm-ethers5`
  - Example: `sa=evm-wagmi`
- `sv=`: SDK version
  - Options: `string`
  - Example: `sv=5.0.0`
- `projectId=`: The project ID for tracking and analytics
  - Options: `string`
  - Example: `projectId=702e2d45...`

### Optional Query Parameters

All API endpoints support the following optional query parameters:

- `env=`: Environment
  - Options: `string`
  - Example: `env=react-native@0.74.1`
- `os=`: Operating System
  - Options: `string`
  - Example: `os=ios@17.4`

## Headers

All API endpoints expect the following simple (i.e. non-preflight-dependent) headers:

- `Accept-Encoding`
  - Options: `gzip | br`
  - Example: `Accept-Encoding: gzip, br`

## API Endpoints

### `POST /v2/wallets`

Fetches wallet data based on following request body provided as a stringified JSON object (`Content-Type: text/plain`).

#### Request Body

```typescript
{
  "offset": number, // Required - The number of items to skip before returning results (e.g. `0`)
  "limit": number, // Required - Number of items to return per request (e.g. `100`)
  "search": string | null, // Optional search term (e.g. `MetaMask`)
  "include": string | null, // Optional comma-separated list of wallet IDs to include
  "exclude": string | null, // Optional comma-separated list of wallet IDs to exclude
  "chains": string | null, // Optional comma-separated list of chain identifiers (e.g. `eip155:1`)
  "platform": string | null // Optional platform filter (`ios` or `android`)
}
```

#### Response

```typescript
{
  "count": number,
  "data": [
    {
      "id": string,
      "name": string,
      "homepage": string,
      "image_id": string,
      "order": number,
      "desktop_link": string | null,
      "webapp_link": string | null,
      "app_store": string | null,
      "play_store": string | null,
      "mobile": {
        deep_link: string | null,
        universal_link: string | null
      }
      "injected": [
        {
          "namespace": string,
          "injected_id": string
        }
      ] | null
    }
    "rdns": string | null
    "chains": string[]
  ]
}
```

### `GET /v2/ios-data`

Returns a list of iOS-facing wallet data, including the public Explorer ID and iOS deep link schema. This allows apps to check exhaustively what wallets are installed.

#### Response

```typescript
{
  "count": number,
  "data": [
    {
      "id": string,
      "ios_schema": string
    }
  ]
}
```

### `GET /v2/android-data`

Returns a list of Android-facing wallet data, including the public Explorer ID and Android App ID. This allows apps to check exhaustively what wallets are installed.

#### Response

```typescript
{
  "count": number,
  "data": [
    {
      "id": string,
      "android_app_id": string
    }
  ]
}
```

### `GET /v2/wallet-image/{size}/{wallet_id}`

Returns the image logo for the specified wallet ID.

#### Route Parameters

- `wallet_id`: The unique identifier for the wallet
- `size`: The predefined image size, described as `{width}x{height}` (see [Known Image Sizes](#known-image-sizes))

#### Response

- A binary image file (Blob)

### `GET /v2/asset-image/network/{size}/{asset_id}`

Fetches images for network logos using well-defined CAIP identifiers.

#### Route Parameters

- `asset_id`: CAIP identifier for the network (e.g., `eip155-1`)
- `size`: The predefined image size, described as `{width}x{height}` (see [Known Image Sizes](#known-image-sizes))

#### Example API Requests

- Ethereum: `GET /v2/asset-image/network/56x56/eip155-1`
- Arbitrum: `GET /v2/asset-image/network/56x56/eip155-42161`
- Avalanche: `GET /v2/asset-image/network/56x56/eip155-43114`
- Binance Smart Chain: `GET /v2/asset-image/network/56x56/eip155-56`
- Fantom: `GET /v2/asset-image/network/56x56/eip155-250`
- Optimism: `GET /v2/asset-image/network/56x56/eip155-10`
- Polygon: `GET /v2/asset-image/network/56x56/eip155-137`
- Gnosis: `GET /v2/asset-image/network/56x56/eip155-100`
- Evmos: `GET /v2/asset-image/network/56x56/eip155-9001`
- ZkSync: `GET /v2/asset-image/network/56x56/eip155-324`
- Filecoin: `GET /v2/asset-image/network/56x56/fil-314`
- IoTeX: `GET /v2/asset-image/network/56x56/eip155-4689`
- Metis: `GET /v2/asset-image/network/56x56/eip155-1088`
- Moonbeam: `GET /v2/asset-image/network/56x56/eip155-1284`
- Moonriver: `GET /v2/asset-image/network/56x56/eip155-1285`
- Zora: `GET /v2/asset-image/network/56x56/eip155-7777777`
- Celo: `GET /v2/asset-image/network/56x56/eip155-42220`
- Base: `GET /v2/asset-image/network/56x56/eip155-8453`
- Aurora: `GET /v2/asset-image/network/56x56/eip155-1313161554`

#### Response

- A binary image file (Blob)

### `GET /v2/asset-image/connector/{size}/{connector_id}`

Fetches image for connector logos using descriptive names.

#### Route Parameters

- `connector_id`: Descriptive name for the connector (e.g., `coinbaseWalletSDK`)
- `size`: The predefined image size, described as `{width}x{height}` (see [Known Image Sizes](#known-image-sizes))

#### Example API Requests

- Coinbase Wallet: `GET /v2/asset-image/connector/56x56/coinbaseWalletSDK`
- Safe: `GET /v2/asset-image/connector/56x56/safe`
- Ledger: `GET /v2/asset-image/connector/56x56/ledger`
- WalletConnect: `GET /v2/asset-image/connector/56x56/wallet-connect`
- Injected (Generic Web3 Injected Connectors): `GET /v2/asset-image/connector/56x56/injected`

#### Response

- A binary image file (Blob)

### `GET /v2/asset-image/token/{size}/{token_symbol}`

Fetches image for known tokens using the token's ticker symbol.

#### Route Parameters

- `token_symbol`: The token's ticker symbol (e.g., `ETH`, `USDC`, `WBTC`)
- `size`: The predefined image size, described as `{width}x{height}` (see [Known Image Sizes](#known-image-sizes))

#### Example API Requests

- Ethereum: `GET /v2/asset-image/token/56x56/ETH`
- USD Coin: `GET /v2/asset-image/token/56x56/USDC`
- Wrapped Bitcoin: `GET /v2/asset-image/token/56x56/WBTC`

#### Response

- A binary image file (Blob)

### GET `/v2/asset-image/currency/{size}/{currency_symbol}`

Fetches image for known fiat currencies using the currency's symbol.

#### Route Parameters

- `currency_symbol`: The currency's symbol (e.g., `USD`, `EUR`, `JPY`)
- `size`: The predefined image size, described as `{width}x{height}` (see [Known Image Sizes](#known-image-sizes))

#### Example API Requests

- US Dollar: `GET /v2/asset-image/currency/56x56/USD`
- Euro: `GET /v2/asset-image/currency/56x56/EUR`
- Japanese Yen: `GET /v2/asset-image/currency/56x56/JPY`

#### Response

- A binary image file (Blob)

### `GET /v2/analytics-config`

Fetches the analytics configuration for the project based on the mandatory `projectId=` query parameter.

#### Response

```typescript
{
  isAnalyticsEnabled: boolean,
}
```

## Known Image Sizes

- `56x56`
- `80x80`
- `112x112`
  `160x160`
