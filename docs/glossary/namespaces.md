# Namespaces

Namespaces are used to specify the chains, methods and events that are intended to be used in a particular session. They establish the minimal requirement for a wallet and a dapp to get paired. There are two types of namespaces,

## Proposal namespaces

A dapp sends a proposal namespace to the wallet for pairing. The proposal namespace contains the list of chains, methods and events that are required for the dapp. The wallet validates if the received proposal namespaces are valid and returns a session namespace as a response if it is valid. If the requested proposal namespaces are not valid, the session cannot be established and the wallet rejects it with an error code that tells the dapp if the proposal namespaces have invalid chains, methods, events or if it was rejected by the user

**Example :** If a dapp wants access to Ethereum Mainnet, Polygon and Cosmos Mainnet - the required chains, methods and events should be mentioned in the proposal namespaces request as follows :

```js
{
  "eip155": {
    "chains": ["eip155:137", "eip155:1"],
    "methods": ["eth_sign"],
    "events": ["accountsChanged"]
  },
  "cosmos": {
    "chains": ["cosmos:cosmoshub-4"],
    "methods": ["cosmos_signDirect"],
    "events": ["someCosmosEvent"]
  }
}
```

## Session namespaces

The dapp validates if the received proposal namespaces comply with the session namespaces. If they comply, a session is established successfully and pairing is completed. If not, the session is not established and all the cached data related to the namespaces are deleted. The session namespace can also choose to provide access to more chains, methods or events that were not a part of the proposal namespaces.

**Example :** The following is an example for a session namespace which complies with the requested proposal namespace example,

```js
{
  "eip155": {
    "accounts": [
      "eip155:137:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb",
      "eip155:1:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb"
    ],
    "methods": ["eth_sign"],
    "events": ["accountsChanged"]
  },
  "cosmos": {
    "accounts": [
      "cosmos:cosmoshub-4:cosmos1t2uflqwqe0fsj0shcfkrvpukewcw40yjj6hdc0"
    ],
    "methods": ["cosmos_signDirect", "personal_sign"],
    "events": ["someCosmosEvent", "proofFinalized"]
  }
}
```

You can also see that `personal_sign` method and `proofFinalized` event are not requested by the proposal namespaces but is still granted by the session namespaces. Hence, session namespaces can grant additional access to more chains, methods and events which were not requested by the proposal namespaces.
