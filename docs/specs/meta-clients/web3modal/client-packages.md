# Client Packages

Principal purpose of client packages is to extend [scaffold](./scaffold-package.md) package and implement provider specific logic i.e. `@web3modal/wagmi` in a way that syncs everything up with [core](./core-package.md) package controllers.

Secondary purpose of this package is to expose itself via front-end framework friendly api's i.e. `@web3modal/wagmi/react` or `@web3modal/wagmi/vue`.

## Overview
Implementation of this package will vary greatly depending on which underlying provider framework is used, so it is not possible to provide concrete reference for implementation. That said, here is a list of best practices to follow and remember:

1. Create a class that extends `Web3ModalScaffold`
2. Make sure each client set's correct unique sdkVersion i.e. `html-wagmi-3.1.0` or `react-ethers-3.0.0`
3. Respect super constructor and implement core controller client methods
4. Make sure you convert inputs and outputs of provider framework methods to ones expected by modal packages (in particular caip compliant formats).
5. Make sure you get initial data like address, network, balance etc. when needed, but also don't forget to add "watchers" for these when they are changed externally.
