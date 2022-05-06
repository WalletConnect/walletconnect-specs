# Error Codes

## REJECTED (CAIP-25)

```sh
case .userRejectedChains: return 5000
case .userRejectedMethods: return 5001
case .userRejectedEvents: return 5002

case .unsupportedChains: return 5100
case .unsupportedMethods: return 5101
case .unsupportedEvents: return 5102
```

## UNAUTHORIZED

```sh
case .unauthorizedMethod: return 3001
case .unauthorizedEvent: return 3002

case .unauthorizedUpdateAccountRequest: return 3003
case .unauthorizedUpdateNamespacesRequest: return 3004
case .unauthorizedUpdateExpiryRequest: return 3005
```

## INVALID

```sh
case .invalidMethod: return 1001
case .invalidEvent: return 1002

case .invalidUpdateAccountsRequest: return 1003
case .invalidUpdateNamespaceRequest: return 1004
case .invalidUpdateExpiryRequest: return 1005
```
