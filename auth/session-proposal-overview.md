# Session proposal overview:
Let's say a dapp wants access to Ethereum Mainnet, Polygon, Cosmos Mainnet.
It issues required methods and events for specific blockchains in form of  proposal namespaces. For example Ethereum Mainnet and Polygon it requires: `eth_sign` method and `accountsChanged` event, and for Cosmos Mainnet it requires: `cosmos_signDirect` method and `someCosmosEvent` event. Let's say that Polygon also has special method `personalSign` and event `chainChanged` not available for Ethereum Mainnet. Dapp can request those only for Polygon via `extensions` field. 


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

Wallet then validates if recieved proposal namespaces are valid. If they are not valid then session cannot be established and is rejected. If they are valid then Wallet decides if it should approve the proposal.

If Wallet doesn't approve then session is rejected. Otherwise, Wallet responds with slighly different structure than proposal namespaces called session namespaces. Instead of list of `chains` it has list of `accounts` that approves given methods and events. If wallet approves session proposal it needs to accept all methods and events in every proposal namespace. If Wallet wants it can give permissions for more methods and events than requested, but never less.


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

Dapp then validates if recieved session namespaces are valid for requested proposal namespaces. If they are valid then session is established. If they are not valid then session cannot be established.  

# Validation test cases

## Controller side validation of incoming proposal namespaces (Wallet)

---
### 1.1. Proposal namespaces mustn't have chains empty
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
### 1.2. Chains must be CAIP-2 compliant
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

### 1.3. Proposal namespace can have no methods and events 
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
### 1.4. TODO: Different origin blockchains can be defined in one namespace
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

### 1.5. TODO: Blockchain can be in mutliple namespaces
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

### 1.6. Proposal namespaces extensions mustn't have chains empty
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

### 1.7. Extensions have optional method and events
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

### 1.8. One invalid namespace makes whole request invalid
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


Czy dodawać walidacje tego co jest wewntarz przestrzeni?? eip155 musi jedynie zawierac eip155:42 itp?

## Non-controller side validation of incoming proposal namespaces (Dapp)
---
### 2.1. Session namespaces mustn't have accounts empty
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
        "chains": [],
        "methods": ["cosmos_signDirect"],
        "events": ["someCosmosEvent"]
    }
}
```
Is valid?: No

Note: Proposal namespace doesn't have any accounts, hence it's invalid

---
### 2.2. Session namespaces addresses must be CAIP-10 compliant
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

### 2.3. Session namespaces must accept all methods and events
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
        "methods": [],
        "events": ["someOtherNotDefinedEvent"]
    }
}
```
Is valid?: No

Note: `eth_sign` method and `accountsChanged` event are missing in the session namespace 

---

### 2.4. Session namespaces must contain at least one account in requested chains
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

### 2.5. Session namespaces can contain multiple accounts for one chain
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

### 2.6. Session namespaces can extend methods and events of proposal namespaces
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

### 2.7. TODO: Multiple accounts from different origin in one namespace
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

### 2.8. TODO: Session namespaces can/musn't contain accounts from chains not defined in proposal namespaces
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

### 2.9. Proposal namespace and session namespaces must have matching namespaces
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

### 2.10. TODO: Merging proposal namespaces into one, logically correct, session namespace (Only if 2.9 is valid)
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

### 2.11. TODO: Expanding proposal namespaces into multiple, logically correct, session namespace (Only if 2.9 is valid)
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

### 2.12. TODO: Extensions merged with to namespace
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