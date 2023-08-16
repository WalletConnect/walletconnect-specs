# Api

Web3Modal has it's own api available at [api.web3modal.com](https://api.web3modal.com) that is used to fetch wallet data from a WalletConnect maintained [wallet & dapp registry](https://walletconnect.com/explorer).

All responses are cached for a minimum of 2h.

## Headers
All api requests must contain following headers. See [types](./types.md)

```ts
interface Headers {
  'x-project-id': string
  'x-sdk-type': SdkType
  'x-sdk-version': SdkVersion
}
```

## Methods

### POST `/getWallets`
Returns total count and wallet data, based on provided body inputs. See [types](./types.md).

```ts
interface Request {
  headers: Headers
  body: {
    page: number
    entries: number
    search?: string
    include?: string[]
    exclude?: string[]
  }
}

interface Response {
  count: number
  data: ApiWallets[]
}
```

### GET `/getWalletImage/:wallet_id`
Returns image logo for requested `wallet_id`. See [types](./types.md).


```ts
interface Request {
  headers: Headers
}

type Response = Blob
```

### GET `/public/getAssetImage/:asset_id`
Returns image for public asset for requested `asset_id`, i.e network logo, token logo or connector logo.

```ts
interface Request {
  headers: Headers
}

type Response = Blob
```
