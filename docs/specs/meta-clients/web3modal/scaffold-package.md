# Scaffold Package
 
Principal purpose of scaffold package is to "glue" [ui](./ui-package.md) and [core](./core-package.md) packages together into full modal experience.

Scaffold package is responsible for creating complete layouts, views, partials and hooking them up to core logic.

Secondary purpose of this package is to export re-usable class (client) to create Web3Modal instance that bootstraps everything up and can be used to extend framework specific classes i.e. Wagmi Web3Modal by abstracting as much of re-usable logic as possible.

## Modal
Core modal components like router and modal shell

## Partials
Complex peaces of ui aka widgets that can be re-used in views

## Views
Individual modal views

## Client
```ts
interface Options {
  networkControllerClient: NetworkControllerClient
  connectionControllerClient: ConnectionControllerClient
  projectId: ProjectId
  sdkVersion: SdkVersion
}

class Web3ModalScaffold {
  // Tracks if async initialization is in progress or resolved
  private initPromise: Promise<void> | undefined

  // Initializes core controllers and bootstrap logic, sets initPromise
  constructor(options: Options): void

  // Awaits initialization, opens modal
  public open(options?: { view: string }): Promise<void>

  // Awaits initialization, closes modal
  public close(): Promise<void>

  // Exposes AccountController.setIsConnected to extending clients
  protected setIsConnected: AccountController['setIsConnected']

  // Exposes AccountController.setCaipAddress to extending clients
  protected setCaipAddress: AccountController['setCaipAddress']

  // Exposes AccountController.setBalance to extending clients
  protected setBalance: AccountController['setBalance']

  // Exposes AccountController.setProfileName to extending clients
  protected setProfileName: AccountController['setProfileName']

  // Exposes AccountController.setProfileImage to extending clients
  protected setProfileImage: AccountController['setProfileImage']

  // Exposes AccountController.resetAccount to extending clients
  protected resetAccount: AccountController['resetAccount']

  // Exposes NetworkController.setCaipNetwork to extending clients
  protected setCaipNetwork: NetworkController['setCaipNetwork']

  // Exposes NetworkController.state.caipNetwork to extending clients
  protected getCaipNetwork: NetworkController['state']['caipNetwork']

  // Exposes NetworkController.setRequestedCaipNetworks to extending clients
  protected setRequestedCaipNetworks: NetworkController['setRequestedCaipNetworks']

  // Exposes NetworkController.getApprovedCaipNetworks to extending clients
  protected getApprovedCaipNetworks: NetworkController['getApprovedCaipNetworks']

  // Exposes NetworkController.resetNetwork to extending clients
  protected resetNetwork: NetworkController['resetNetwork']

  // Exposes ConnectorController.setConnectors to extending clients
  protected setConnectors: ConnectorController['setConnectors']

  // Exposes ConnectorController.resetWcConnection to extending clients
  protected resetWcConnection: ConnectorController['resetWcConnection']
}
```