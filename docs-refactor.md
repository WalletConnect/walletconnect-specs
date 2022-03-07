# Docs Refactor

## Before

- Introduction
- Quick Start
  - Dapps
    -   Standalone Client
    -   NodeJS Client
    -   React-Native
    -   Web3 Provider
  - Wallets
    -   Kotlin Client (Android)
    -   Swift Client (iOS)
    -   React-Native Client
- Technical Specification


## After

- Introduction
- Javascript
  -   Guides
          - Dapps on Browser
          - Dapps on React-Native
          - Wallets on React-Native
  -   APIs
          -   Protocol Client (@walletconnect/client)
          -   Ethereum Provider (@walletconnect/ethereum-provider)
          -   Cosmos Provider (@walletconnect/cosmos-provider)
          -   Solana Provider (@walletconnect/solana-provider)
          -   UI Modal (@walletconnect/ui-modal)
            -     Connect View (displays qrcode with URI or deep links for mobile)
            -     Pairing View (displays active pairings to connect with or create new pairing)
            -     Pop-up View (displays active sessions and enable user to connect/disconnect)
          -   WalletConnect SDK (walletconnect)
  -   React-Native
      -   Compatibility (nodejs polyfills)
      -   etc  
- Swift 
- Kotlin



