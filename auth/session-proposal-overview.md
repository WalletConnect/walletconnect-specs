# Session proposal overview:
Let's say a dapp wants access to Ethereum Mainnet, Polygon, Cosmos Mainnet.
It specifies the proposed execution environment for each blockchain in the form of namespaces. For example, for Ethereum Mainnet and Polygon, it requires: `eth_sign` method and `accountsChanged` event, and, for Cosmos Mainnet, it requires: `cosmos_signDirect` method and `someCosmosEvent` event. Let's say that Polygon also has a special method `personalSign` and an event `chainChanged` not available in Ethereum Mainnet. The Dapp can require chain-exclusive parameters via the `extensions` field. 


## Example proposal namespaces request

```json
{
    "eip155":{
        "chains": ["eip155:137", "eip155:1"],
        "methods": ["eth_sign"],
        "events": ["accountsChanged"],
        "extensions": [
            {
                "chains":["eip155:137"],
                "method":["personalSign"],
                "events":["chainChanged"]
            }
        ]
    },
    "cosmos":{
        "chains": ["cosmos:cosmoshub-4"],
        "methods": ["cosmos_signDirect"],
        "events": ["someCosmosEvent"]
    }
}
```

The Wallet then validates if the received proposal namespaces are valid. If they are not valid, then the session cannot be established and the Wallet rejects it with a `1006` code that tells the Dapp that the proposal namespaces are invalid. If they are valid, then the Wallet if free to decide whether to approve the proposal, or reject it.

If the Wallet (or the user) does NOT approve the session, then it is rejected. Otherwise, the Wallet responds with a slightly different namespace schema: session namespaces. Instead of having a list of `chains`, it has list of `accounts` compatible with the given methods and events. If the Wallet approves a session proposal, it needs to accept all methods and events of all proposal namespaces. If needed, the Wallet can add permissions for more methods and events than the ones requested, but never less.


## Example session namespaces response
``` json
{
    "eip155":{
        "accounts": ["eip155:137:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb", "eip155:1:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb"],
        "methods": ["eth_sign"],
        "events": ["accountsChanged"],
        "extensions": [
            {
                "accounts":["eip155:137:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb"],
                "method":["personalSign"],
                "events":["chainChanged"]
            }
        ]
    },
    "cosmos":{
        "accounts": ["cosmos:cosmoshub-4:cosmos1t2uflqwqe0fsj0shcfkrvpukewcw40yjj6hdc0"],
        "methods": ["cosmos_signDirect", "personal_sign"],
        "events": ["someCosmosEvent", "proofFinalized"]
    }
}
```

The Dapp then validates if the received session namespaces comply with the requested proposal namespaces. If so, the session is established / settled. If not, the session is not established and MUST delete all related cached data.

# Validation test cases

## Controller side validation of incoming proposal namespaces (Wallet)

---
### 1.1. Proposal namespaces MUST NOT have chains empty
Requested proposal namespaces:
```json
{
    "cosmos":{
        "chains": [],
        "methods": ["cosmos_signDirect"],
        "events": ["someCosmosEvent"]
    }
}
```
Is valid?: No

Note: Proposal namespace doesn't have any chains, hence it's invalid

---
### 1.2. Chains MUST be CAIP-2 compliant
Requested proposal namespaces:
```json
{
    "eip155":{
        "chains": ["42"],
        "methods": ["eth_sign"],
        "events": ["accountsChanged"]
    }
}
```
Is valid?: No

Note: `42` is not CAIP-2 compliant. `eip155:42` is CAIP-2 compliant.

---

### 1.3. Proposal namespace methods and events MAY be empty
Requested proposal namespaces:
```json
{
    "eip155":{
        "chains": ["eip155:1"],
        "methods": [],
        "events": []
    }
}
```
Is valid?: Yes

---
### 1.4. TODO: Different origin blockchains MAY be defined in one namespace
Requested proposal namespaces:
```json
{
    "someSpecialKey":{
        "chains": ["eip155:1", "cosmos:cosmoshub-4", "bip122:12a765e31ffd4059bada1e25190f6e98", "cosmos:Binance-Chain-Tigris"],
        "methods": ["personalSign"],
        "events": ["chainChanged"]
    }
}
```
Is valid?: Yes/No?

---

### 1.5. TODO: Blockchain MAY be in mutliple namespaces
Requested proposal namespaces:
```json
{
    "eip155":{
        "chains": ["eip155:1", "eip155:10"],
        "methods": ["personalSign"],
        "events": []
    },
    "someSpecialKey":{
        "chains": ["eip155:1"],
        "methods": ["eth_getAccounts"],
        "events": []
    }
}
```
Is valid?: Yes/No?

---

### 1.6. Proposal namespaces extensions MUST NOT have chains empty
Requested proposal namespaces:
```json
{
    "eip155":{
        "chains": ["eip155:1", "eip155:137"],
        "methods": ["personalSign"],
        "events": [],
        "extensions": [
            {
                "chains" : [],
                "methods": ["eth_getAccounts"],
            }
        ]
    }
}
```
Is valid?: No

Note: Proposal namespace extension doesn't have any chains, hence it's invalid

---

### 1.7. Extensions method and events are OPTIONAL
Requested proposal namespaces:
```json
{
    "eip155":{
        "chains": ["eip155:1", "eip155:137", "eip155:10"],
        "methods": ["personalSign"],
        "events": [],
        "extensions": [
            {
                "chains" : ["eip155:137"],
                "methods": ["eth_getAccounts"],
            },
            {
                "chains" : ["eip155:1"],
                "events": ["chainChanged"],
            },
            {
                "chains" : ["eip155:10"],
            }
        ]
    }
}
```
Is valid?: Yes

Note: First extension is missing `events` field but remains valid. Second extensions is missing `methods` field but remains valid. Third extensions only has `chains` field but due to `method` and `events` field being optional it remains valid, even though it has no logical value. 

---

### 1.8. All namespaces MUST be valid
Requested proposal namespaces:
```json
{
    "eip155":{
        "chains": ["eip155:1"],
        "methods": ["personalSign"],
        "events": []
    },
    "cosmos":{
        "chains": [],
        "methods": [],
        "events": []
    }
}
```
Is valid?: No

Note: Even though, the first proposal namespace is valid the second being invalid makes whole proposal invalid

---

## Non-controller side validation of incoming proposal namespaces (Dapp)
---
### 2.1. Session namespaces MUST NOT have accounts empty
Requested proposal namespaces:
```json
{
    "cosmos": {
        "chains": ["cosmos:cosmoshub-4"],
        "methods": ["cosmos_signDirect"],
        "events": ["someCosmosEvent"]
    }
}
```
Received session namespaces:
```json
{
    "cosmos": {
        "accounts": [],
        "methods": ["cosmos_signDirect"],
        "events": ["someCosmosEvent"]
    }
}
```
Is valid?: No

Note: Proposal namespace doesn't have any accounts, hence it's invalid

---
### 2.2. Session namespaces addresses MUST be CAIP-10 compliant
Requested proposal namespaces:
```json
{
    "eip155": {
        "chains": ["eip155:1"],
        "methods": ["eth_sign"],
        "events": ["accountsChanged"]
    }
}
```
Received session namespaces:
```json
{
    "eip155": {
        "accounts": ["0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb"],
        "methods": ["eth_sign"],
        "events": ["accountsChanged"]
    }
}
```
Is valid?: No

Note: `0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb` is not CAIP-10 compliant.
`eip155:1:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb` is CAIP-10 compliant

---

### 2.3. Session namespaces MUST accept all methods 
Requested proposal namespaces:
```json
{
    "eip155": {
        "chains": ["eip155:1"],
        "methods": ["eth_sign"],
        "events": []
    }
}
```
Received session namespaces:
```json
{
    "eip155": {
        "accounts": ["eip155:1:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb"],
        "methods": [],
        "events": []
    }
}
```
Is valid?: No

Note: `eth_sign` method is missing in the session namespace 

---

### 2.4. Session namespaces MUST contain at least one account in requested chains
Requested proposal namespaces:
```json
{
    "eip155": {
        "chains": ["eip155:1", "eip155:10"],
        "methods": ["eth_sign"],
        "events": ["accountsChanged"]
    }
}
```
Received session namespaces:
```json
{
    "eip155": {
        "accounts": ["eip155:1:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb"],
        "methods": ["eth_sign"],
        "events": ["accountsChanged"]
    }
}
```
Is valid?: No

Note: There is no account specified for `eip155:10`

---

### 2.5. Session namespaces MAY contain multiple accounts for one chain
Requested proposal namespaces:
```json
{
    "eip155": {
        "chains": ["eip155:1"],
        "methods": ["eth_sign"],
        "events": ["accountsChanged"]
    }
}
```
Received session namespaces:
```json
{
    "eip155": {
        "accounts": ["eip155:1:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb", "eip155:1:0x25caCa7f7Bf3A77b1738A8c98A666dd9e4C69A0C",
        "eip155:1:0x2Fe1cC9b1DCe6E8e16C48bc6A7ABbAB3d10DA954",
        "eip155:1:0xEA674fdDe714fd979de3EdF0F56AA9716B898ec8",
        "eip155:1:0xEB2F31B0224222D774541BfF89A221e7eb15a17E"],
        "methods": ["eth_sign"],
        "events": ["accountsChanged"]
    }
}
```
Is valid?: Yes

---

### 2.6. Session namespaces MAY extend methods and events of proposal namespaces
Requested proposal namespaces:
```json
{
    "eip155": {
        "chains": ["eip155:1"],
        "methods": ["eth_sign"],
        "events": ["accountsChanged"]
    }
}
```
Received session namespaces:
```json
{
    "eip155": {
        "accounts": ["eip155:1:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb"],
        "methods": ["eth_sign", "personalSign"],
        "events": ["accountsChanged", "someEvent"]
    }
}
```
Is valid?: Yes

---

### 2.7. TODO: Namespace MAY contain blockchain from different namespace
Requested proposal namespaces:
```json
{
    "someSpecialKey": {
        "chains": ["eip155:1", "cosmos:cosmoshub-4"],
        "methods": ["personalSign"],
        "events": ["accountsChanged"]
    }
}
```
Received session namespaces:
```json
{
    "someSpecialKey": {
        "accounts": ["eip155:1:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb", "cosmos:cosmoshub-4:cosmos1t2uflqwqe0fsj0shcfkrvpukewcw40yjj6hdc0"],
        "methods": ["personalSign"],
        "events": ["accountsChanged"]
    }
}
```
Is valid?: Yes/No?

---

### 2.8. TODO: Session namespaces MAY/MUST NOT contain accounts from chains not defined in proposal namespaces
Requested proposal namespaces:
```json
{
    "eip155": {
        "chains": ["eip155:1"],
        "methods": ["eth_sign"],
        "events": ["accountsChanged"]
    }
}
```
Received session namespaces:
```json
{
    "eip155": {
        "accounts": ["eip155:1:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb", "eip155:42:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb"],
        "methods": ["eth_sign"],
        "events": ["accountsChanged"]
    }
}
```
Is valid?: Yes/No

---

### 2.9. Proposal namespace and session namespaces MUST have matching namespaces
Requested proposal namespaces:
```json
{
    "eip155": {
        "chains": ["eip155:137", "eip155:1"],
        "methods": ["eth_sign"],
        "events": ["accountsChanged"]
    },
    "cosmos":{
        "chains": ["cosmos:cosmoshub-4"],
        "methods": ["cosmos_signDirect"],
        "events": ["someCosmosEvent"]
    }

```
Received session namespaces:
```json
{
    "someSpecialKey": {
        "accounts": ["eip155:1:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb","eip155:137:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb", "cosmos:cosmoshub-4:cosmos1t2uflqwqe0fsj0shcfkrvpukewcw40yjj6hdc0"],
        "methods": ["eth_sign", "cosmos_signDirect"],
        "events": ["accountsChanged", "someCosmosEvent"]
    }
}
```
Is valid?: Yes/No

---

### 2.10. TODO: Proposal namespaces MAY/MUST NOT be merged into one, logically correct, session namespace (Only if 2.9 is valid)
Requested proposal namespaces:
```json
{
    "eip155": {
        "chains": ["eip155:137", "eip155:1"],
        "methods": ["eth_sign"],
        "events": ["accountsChanged"]
    },
    "cosmos":{
        "chains": ["cosmos:cosmoshub-4"],
        "methods": ["cosmos_signDirect"],
        "events": ["someCosmosEvent"]
    }

```
Received session namespaces:
```json
{
    "someSpecialKey": {
        "accounts": ["eip155:1:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb","eip155:137:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb", "cosmos:cosmoshub-4:cosmos1t2uflqwqe0fsj0shcfkrvpukewcw40yjj6hdc0"],
        "methods": ["eth_sign", "cosmos_signDirect"],
        "events": ["accountsChanged", "someCosmosEvent"]
    }
}
```
Is valid?: Yes/No

---

### 2.11. TODO: Proposal namespaces MAY/MUST NOT be expanded into multiple, logically correct, session namespace (Only if 2.9 is valid)
Requested proposal namespaces:
```json
{
    "eip155": {
        "chains": ["eip155:137", "eip155:1"],
        "methods": ["eth_sign"],
        "events": ["accountsChanged"]
    }
}
```

Received session namespaces:
```json
{
    "eip155": {
        "accounts": ["eip155:1:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb"],
        "methods": ["eth_sign"],
        "events": ["accountsChanged"]
    },
    "eip155ButBetter":{
        "accounts": ["eip155:137:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb"],
        "methods": ["eth_sign"],
        "events": ["accountsChanged"]
    }
}
```
Is valid?: Yes/No

---

### 2.12. TODO: Extensions MAY/MUST NOT be merged into namespace
Requested proposal namespaces:
```json
{
    "eip155": {
        "chains": ["eip155:137", "eip155:1"],
        "methods": ["eth_sign"],
        "events": ["accountsChanged"],
        "extensions":[
            {
                "chains": ["eip155:137"],
                "methods": ["personalSign"]
            }
        ]
    }
}
```

Received session namespaces:
```json
{
    "eip155": {
        "accounts": ["eip155:1:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb", "eip155:137:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb"],
        "methods": ["eth_sign", "personalSign"],
        "events": ["accountsChanged"],
    }
}
```
Is valid?: Yes/No

---


### 2.13. Session namespaces MUST accept all events 
Requested proposal namespaces:
```json
{
    "eip155": {
        "chains": ["eip155:1"],
        "methods": [],
        "events": ["chainChanged"]
    }
}
```
Received session namespaces:
```json
{
    "eip155": {
        "accounts": ["eip155:1:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb"],
        "methods": [],
        "events": []
    }
}
```
Is valid?: No

Note: `chainChanged` event is missing in the session namespace 

---

### 2.14. TODO: Session namespaces extensions MAY/MUST NOT extend methods and events of proposal namespaces extensions
Requested proposal namespaces:
```json
{
    "eip155": {
        "chains": ["eip155:1", "eip155:137"],
        "methods": [],
        "events": ["chainChanged"],
        "extensions": [
            {
                "chains": ["eip155:137"],
                "methods": ["eth_sign"]
            }
        ]
    }
}
```
Received session namespaces:
```json
{
    "eip155": {
        "accounts": ["eip155:1:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb","eip155:137:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb"],
        "methods": [],
        "events": ["chainChanged"],
        "extensions": [
            {
                "accounts": ["eip155:137:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb"],
                "methods": ["eth_sign", "personalSign"],
                "events": ["accountsChanged"]
            }
        ]
    }
}
```
Is valid?: Yes/No

---

### 2.15. TODO: Session namespaces extensions MAY/MUST NOT contain accounts from chains not defined in proposal namespaces extensions
Requested proposal namespaces:
```json
{
    "eip155": {
        "chains": ["eip155:1", "eip155:137"],
        "methods": ["eth_sign"],
        "events": ["accountsChanged"],
        "extensions": [
            {
                "chains": ["eip155:137"],
                "methods": ["personalSign"],
                "events": ["accountsChainged"]
            }
        ]
    }
}
```
Received session namespaces:
```json
{
    "eip155": {
        "accounts": ["eip155:1:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb", "eip155:137:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb"],
        "methods": ["eth_sign"],
        "events": ["accountsChanged"],
        "extensions": [
            {
                "accounts": ["eip155:137:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb", "eip155:42:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb"],
                "methods": ["personalSign"],
                "events": ["accountsChainged"]
            }
        ]
    }
}
```
Is valid?: Yes/No

---


### 2.16. TODO: Session namespaces MAY/MUST NOT add extensions not defined in proposal namespaces extensions
Requested proposal namespaces:
```json
{
    "eip155": {
        "chains": ["eip155:1", "eip155:137"],
        "methods": ["eth_sign"],
        "events": ["accountsChanged"]
    }
}
```
Received session namespaces:
```json
{
    "eip155": {
        "accounts": ["eip155:1:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb", "eip155:137:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb"],
        "methods": ["eth_sign"],
        "events": ["accountsChanged"],
        "extensions": [
            {
                "accounts": ["eip155:137:0xab16a96d359ec26a11e2c2b3d8f8b8942d5bfcdb"],
                "methods": ["personalSign"],
                "events": ["accountsChainged"]
            }
        ]
    }
}
```
Is valid?: Yes/No

---