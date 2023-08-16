# Core package

Principal purpose of core package is to define and expose controllers (singletons) that keep track of their reactive state and expose actions that allow updating on it. Some controllers proxy to clients via `_client`.

Secondary purpose of this package is to expose shared utilities related to "core logic".

## AccountController

```ts
interface State {
  isConnected: boolean
  caipAddress?: CaipAddress
  address?: string
  balance?: string
  balanceSymbol?: string
  profileName?: string
  profileImage?: string
}

interface AccountController {
  state: State

  // Subscribe to all state changes
  subscribe: (callback: (newState: State): void): unsubscribe()

  // Subscribe to specific state key change
  subscribeKey: (key: string, callback: (newState: State): void): unsubscribe()

  // Set isConnected value
  setIsConnected(isConnected: State['isConnected']): void

  // Set caipAddress, address values
  setCaipAddress(caipAddress: State['caipAddress']): void

  // Set balance, balanceSymbol values
  setBalance(
    balance: State['balance'], 
    balanceSymbol: State['balanceSymbol']
  ): void

  // Set profileName
  setProfileName(profileName: State['profileName']): void

  // Set profileImage
  setProfileImage(profileImage: State['profileImage']): void

  // Reset account state to default
  resetAccount(): void
}
```

## ApiController

```ts
interface State {
  projectId: ProjectId
  sdkVersion: SdkVersion
  page: number
  count: number
  recommended: ApiWallet[]
  wallets: ApiWallet[]
  search: ApiWallet[]
}

interface ApiController {
  state: State

  // Subscribe to all state changes
  subscribe: (callback: (newState: State): void): unsubscribe()

  // Subscribe to specific state key change
  subscribeKey: (key: string, callback: (newState: State): void): unsubscribe()

  // Set projectId value
  setProjectId(projectId: State['projectId']): void

  // Set sdkVersion value
  setSdkVersion(sdkVersion: State['sdkVersion']): void

  // Generates api headers from state values
  getApiHeaders(): ApiHeaders

  // Fetch wallet image (GET /getWalletImage), update AssetController state
  fetchWalletImage(imageId: string): Promise<Blob>

  // Fetch network image (GET /public/getAssetImage), update AssetController state
  fetchNetworkImage(imageId: string): Promise<Blob>

  // Fetch connector image (GET /public/getAssetImage), update AssetController state
  fetchConnectorImage(imageId: string): Promise<Blob>

  // Fetch token image (GET /public/getAssetImage), update AssetController state
  fetchTokenImage(imageId: string): Promise<Blob>

  // Fetch recommendedWallets (POST /getWallets), update recommended value
  fetchRecommendedWallets(): Promise<{count: number, data: ApiWallet[]}>

  // Fetch wallets (POST /getWallets), update wallets, count, page values
  fetchWallets(page: number): Promise<{count: number, data: ApiWallet[]}>

  // Search wallets (POST /getWallets), update search value
  searchWallets(search: string): Promise<{count: number, data: ApiWallet[]}>
}
```

## AssetController
```ts
interface State {
  walletImages: Record<string, string>
  networkImages: Record<string, string>
  connectorImages: Record<string, string>
  tokenImages: Record<string, string>
}

interface AssetController {
  state: State

  // Subscribe to all state changes
  subscribe: (callback: (newState: State): void): unsubscribe()

  // Subscribe to specific state key change
  subscribeKey: (key: string, callback: (newState: State): void): unsubscribe()

  // Set walletImages value
  setWalletImages(key: string, value: string): void

  // Set networkImages value
  setNetworkImages(key: string, value: string): void

  // Set connectorImages value
  setConnectorImages(key: string, value: string): void

  // Set tokenImages value
  setTokenImages(key: string, value: string): void
}
```

## ConnectionController
```ts
interface Client {
  connectWalletConnect: (onUri: (uri: string): void): Promise<void>
  disconnect: (): Promise<void>
  connectExternal?: (id: string): Promise<void>
  connectInjected?: (): Promise<void>
  checkInjectedInstalled?: (ids?: string[]): boolean
}

interface State {
  _client?: Client
  wcUri?: string
  wcPromise?: Promise<void>
  wcPairingExpiry?: number
  wcLinking?: {
    href: string
    name: string
  }
}

interface ConnectionController {
  state: State

  // Subscribe to all state changes
  subscribe: (callback: (newState: State): void): unsubscribe()

  // Subscribe to specific state key change
  subscribeKey: (key: string, callback: (newState: State): void): unsubscribe()

  // Get _client safely
  _getClient(): Client
  
  // Set _client value
  setClient(client: Client): void

  // Use _client to connect and set, wcPromise, wcUri, wcPairingExpiry
  connectWalletConnect(): Promise<void>

  // Use _client to connect to external provider i.e. coinbase
  connectExternal(): Promise<void>

  // Use _client to connect to injected provider i.e. metamask extension
  connectInjected(): Promise<void>

  // Check if given id's of injected wallets are installed
  checkInjectedInstalled(ids?: string[]): Promise<boolean>

  // Reset wcUri, wcPairingExpiry, wcPromise, wcLinking state
  resetWcConnection(): void

  // Set wcLinking value
  setWcLinking(wcLinking: State['wcLinking']): void

  // Use _client to disconnect
  disconnect(): Promise<void>
}
```

## ConnectorController
```ts
interface State {
  connectors: Connector[]
}

interface ConnectorController {
  state: State

  // Subscribe to all state changes
  subscribe: (callback: (newState: State): void): unsubscribe()

  // Subscribe to specific state key change
  subscribeKey: (key: string, callback: (newState: State): void): unsubscribe()

  // Set connectors value
  setConnectors(connectors: State['connectors']): void

  // Add connector to connectors
  addConnector(connector: Connector): void

  // Remove connector by id
  removeConnector(connectorId: Connector['id']): void
}
```

## ModalController
```ts
interface State {
  open: boolean
}

interface ModalController {
  state: State

  // Subscribe to all state changes
  subscribe: (callback: (newState: State): void): unsubscribe()

  // Subscribe to specific state key change
  subscribeKey: (key: string, callback: (newState: State): void): unsubscribe()

  // Open modal, set open value, optionally set initial view (update RouterController)
  open(options?: { view: string }): void

  // Close modal, set open value
  close(): void
}
```

## NetworkController
```ts
interface Client {
  switchCaipNetwork: (network: CaipNetwork): Promise<void>
  getApprovedCaipNetworksData: (): Promise<{
    approvedCaipNetworkIds: CaipNetworkId[]
    supportsAllNetworks: boolean
  }>
}

interface State {
  supportsAllNetworks: boolean
  _client?: Client
  caipNetwork?: CaipNetwork
  requestedCaipNetworks?: CaipNetwork[]
  approvedCaipNetworkIds?: CaipNetworkId[]
}

interface NetworkController {
  state: State

  // Subscribe to all state changes
  subscribe: (callback: (newState: State): void): unsubscribe()

  // Subscribe to specific state key change
  subscribeKey: (key: string, callback: (newState: State): void): unsubscribe()

  // Get _client safely
  _getClient(): Client

  // Set _client value
  setClient(client: Client): void

  // Set caipNetwork value
  setCaipNetwork(caipNetwork: State['caipNetwork']): void

  // Set requestedCaipNetworks value
  setRequestedCaipNetworks(requestedCaipNetworks: State['requestedCaipNetworks']): void

  // Use _client to get approved networks data and set, supportsAllNetworks, approvedCaipNetworkIds
  getApprovedCaipNetworksData(): Promise<void>

  // Use _client to switch network, update caipNetwork value
  switchActiveNetwork(caipNetwork: State['caipNetwork']): Promise<void>

  // Reset state, keep _client
  resetNetwork(): void
}
```

## RouterController
```ts
interface State {
  view: string
  history: string[]
  data?: {
    connector?: Connector
    wallet?: ApiWallet
    network?: CaipNetwork
  }
}

interface RouterController {
  state: State

  // Subscribe to all state changes
  subscribe: (callback: (newState: State): void): unsubscribe()

  // Subscribe to specific state key change
  subscribeKey: (key: string, callback: (newState: State): void): unsubscribe()

  // Push new view to history, update view, data and history value
  push(view: State['view'], data?: State['data']): void

  // Reset state to include only new view
  reset(view: State['view']): void

  // Replace current view
  replace(view: State['view']): void

  // Go back to previous view
  goBack(): void
}
```

## SnackController
```ts
interface State {
  message?: string
  variant: 'error' | 'success'
  open: boolean
}

interface SnackController {
  state: State

  // Subscribe to all state changes
  subscribe: (callback: (newState: State): void): unsubscribe()

  // Subscribe to specific state key change
  subscribeKey: (key: string, callback: (newState: State): void): unsubscribe()

  // Show success snack, update message, variant and open values
  showSuccess(message: State['message']): void

  // Show error snack, update message, variant and open values
  showError(message: State['message']): void

  // Hide snack, update open value
  hide(): void
}
```
