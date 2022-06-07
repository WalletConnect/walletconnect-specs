# Error Codes

## REJECTED (CAIP-25)

```sh
case .userRejected return 5000
case .userRejectedChains: return 5001
case .userRejectedMethods: return 5002
case .userRejectedEvents: return 5003

case .unsupportedChains: return 5100
case .unsupportedMethods: return 5101
case .unsupportedEvents: return 5102
case .unsupportedAccounts: return 5103
case .unsupportedNamespaceKey: return 5104
```

## UNAUTHORIZED

```sh
case .unauthorizedMethod: return 3001
case .unauthorizedEvent: return 3002

case .unauthorizedUpdateRequest: return 3003
case .unauthorizedExtendRequest: return 3004
```

## INVALID

```sh
case .invalidMethod: return 1001
case .invalidEvent: return 1002

case .invalidUpdateRequest: return 1003
case .invalidExtendRequest: return 1004
```

## REASON

```sh
case .userDisconnected: return 6000
```

## REASON
```sh
case .sessionSettlementFailed: return 7000
```
