# Types

```ts
// CAIP Types
type CaipAddress = `${string}:${string}:${string}`

type CaipNetworkId = `${string}:${string}`

type CaipNetwork = {
  id: CaipNetworkId
  name?: string
  imageId?: string
}

// General Types
type ProjectId = string

type SdkType = 'wcm' | 'w3m'

type SdkVersion = `${string}-${string}-${string}` // react-wagmi-3.0.0

// Connector Types
type ConnectorType = 'EXTERNAL' | 'WALLET_CONNECT' | 'INJECTED' | 'ANNOUNCED'

interface Connector {
  id: string
  type: ConnectorType
  name?: string
  imageId?: string
}
```