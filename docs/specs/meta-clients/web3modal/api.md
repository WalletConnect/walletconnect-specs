
# Web3Modal API v2

> For the deprecated v1 specification, see [here (TODO)](#todo).

## Query Parameters

All API requests can now use query parameters instead of custom headers for SDK details, making integration simpler and more compatible with common HTTP clients and browser caching mechanisms.

### Mandatory Query Parameters

- `st=`: SDK type
  - Options: `w3m | wcm`
  - Example: `st=w3m`
- `sv=`: SDK version
  - Options: `${framework}-${library}-${version}`
  - Example: `react-wagmi-4.0.0`
- `projectId=`: The project ID for tracking and analytics
  - Options: `string`
  - Example: `projectId=702e2d45...`

## API Endpoints

### GET `/v2/wallets`

Fetches wallet data based on provided query parameters.

#### Query Parameters

- `page`: The page number of results (e.g., `1`)
- `entries`: Number of entries per page (e.g., `100`)
- `search`: Optional search term (e.g., `MetaMask`)
- `include`: Optional comma-separated list of wallet IDs to include
- `exclude`: Optional comma-separated list of wallet IDs to exclude
- `chains`: Optional comma-separated list of chain identifiers (e.g., `eip155:1`)
- `platform`: Optional platform filter (`ios` or `android`)

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
      "mobile_link": string | null,
      "desktop_link": string | null,
      "webapp_link": string | null,
      "app_store": string | null,
      "play_store": string | null,
      "injected": [
        {
          "namespace": string,
          "injected_id": string
        }
      ] | null
    }
  ]
}
```

### GET `/v2/wallet-image/{wallet_id}`

Returns the image logo for the specified wallet ID.

#### Query Parameters

- `wallet_id`: The unique identifier for the wallet

#### Response

- A binary image file (Blob)

### GET `/v2/public/asset-image/network/{asset_id}`

Fetches image for public assets like network logos using well-defined CAIP identifiers.

#### Query Parameters

- `asset_id`: CAIP identifier for the network (e.g., `eip155-1`)

#### Example API Requests

- Ethereum: `GET /v2/public/asset-image/network/eip155-1`
- Arbitrum: `GET /v2/public/asset-image/network/eip155-42161`
- Avalanche: `GET /v2/public/asset-image/network/eip155-43114`
- Binance Smart Chain: `GET /v2/public/asset-image/network/eip155-56`
- Fantom: `GET /v2/public/asset-image/network/eip155-250`
- Optimism: `GET /v2/public/asset-image/network/eip155-10`
- Polygon: `GET /v2/public/asset-image/network/eip155-137`
- Gnosis: `GET /v2/public/asset-image/network/eip155-100`
- Evmos: `GET /v2/public/asset-image/network/eip155-9001`
- ZkSync: `GET /v2/public/asset-image/network/eip155-324`
- Filecoin: `GET /v2/public/asset-image/network/fil-314`
- IoTeX: `GET /v2/public/asset-image/network/eip155-4689`
- Metis: `GET /v2/public/asset-image/network/eip155-1088`
- Moonbeam: `GET /v2/public/asset-image/network/eip155-1284`
- Moonriver: `GET /v2/public/asset-image/network/eip155-1285`
- Zora: `GET /v2/public/asset-image/network/eip155-7777777`
- Celo: `GET /v2/public/asset-image/network/eip155-42220`
- Base: `GET /v2/public/asset-image/network/eip155-8453`
- Aurora: `GET /v2/public/asset-image/network/eip155-1313161554`

#### Response

- A binary image file (Blob)

### GET `/v2/public/asset-image/connector/{connector_id}`

Fetches image for connector logos using descriptive names.

#### Query Parameters

- `connector_id`: Descriptive name for the connector (e.g., `coinbase-wallet`)

#### Example API Requests

- Coinbase Wallet: `GET /v2/public/asset-image/connector/coinbase-wallet`
- Safe: `GET /v2/public/asset-image/connector/safe`
- Ledger: `GET /v2/public/asset-image/connector/ledger`
- WalletConnect: `GET /v2/public/asset-image/connector/wallet-connect`
- Injected (Generic Web3 Injected Connectors): `GET /v2/public/asset-image/connector/injected`

#### Response

- A binary image file (Blob)



