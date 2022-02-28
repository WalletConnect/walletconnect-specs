# WC v2.0-rc protocol – The Refactor

# Context
In v1.0 the session and pairing were coupled which meant that a URI was shared to retrieve the session proposal and it established the pairing simultaneously. However that also meant that a new session would require establishing a pairing again.

In v2.0 the session and pairing are decoupled which means that a URI is shared to construct a pairing proposal and only after settling the pairing then the dapp can propose a session using that pairing.

## Problem
Given the new pattern with v2.0 protocol we have a far more interactive protocol that negotiates the pairing exclusively for session proposal transmission. This is very useful because a pairing can be proposed independently from the session and they exist in parallel.

However this introduced a new problem which didn’t exist with v1.0. When using deep linking or universal linking to connect a session with a new pairing on v2.0 protocol this URI is shared by swiping between a dapp and a wallet. 

This swipe puts the dapp (usually in a browser window) on the background which means that the websocket is then killed and it can’t receive the response for the pairing proposal published by the wallet. 

Thus if the dapp never receives the pairing response that means it also will never publish the session proposal meaning that the wallet is redirected with the URI but won’t prompt the user for approval.

## Goal
The solution is to find a protocol change that inherits the same capability of v1.0 to propose session and pairing simultaneously but also persists the decoupled property of v2.0 protocol

# Proposal
### Current Design
Currently the full settlement of a new session between two apps happens through a new pairing proposal that can be fetched under the pairing proposal topic shared from the URI. These are the steps the clients take:
1. Dapp creates pairing proposal
2. Dapp shares URI with topic A with publicKey to encrypt topic B
3. Wallet receives pairing proposal from URI
4. Wallet settles pairing with topic B using derived key 1
5. Wallet sends approval to topic A (pair())
6. Wallet updates state (aka metadata) of topic B after settlement
7. Dapp receives approval on topic A
8. Dapp settles pairing with topic B using derived key 1
9. Dapp sends acknowledgment on topic A
10. Dapp receives updated metadata on topic B
11. Dapp sends session proposal on topic B
12. Dapp listens to topic C for approval using derived key 1
13. Wallet receives session proposal from topic C
14. Wallet settles session with topic D using derived key 2
15. Wallet sends approval to topic C
16. Dapp receives approval on topic C
17. Dapp settles session with topic D using derived key 2
18. Dapp sends acknowledgment on topic C

### Proposed Design
The proposed design removes all negotiation for pairing and instead instantly generates a secure channel using a symmetric key:
1. Dapp generates pairing URI with topic A, relayA, symmetric key
2. Dapp sends session proposal to topic A with publicKey, relayB, permissions and metadata
3. Dapps shares URI with topic A with topic A, relayA, symmetric key for encrypt topic A
4. Wallet pairs using shared URI topic A and symmetric key
5. Wallet receives session proposal from topic A
6. Wallet settles session with topic B using derived key
7. Wallet sends approval to topic A with relayB, publicKey to encrypt topic B
8. Wallet sends settlement payload to topic  B with state, permissions and metadata
9. Dapp receives approval on topic A
10. Dapp settles session with topic B using derived key
11. Dapp receives settlement on topic B
12. Dapp sends acknowledgment on topic B

## Design Comparison
The proposal removes significantly more steps and payloads that need to be sent across to go. 

It uses less ephemeral topics that need to be subscribed and only has one derived key to do so. 

Additionally it can re-use the pairing to send future session proposals without much negotiation.

Finally the proposal fixes the issue of requiring the Dapp to be online to send a session proposal after the user paired with the Wallet redirect from the URI.

# Architecture

## Pairing URI
### Schema ([EIP-1328](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-1328.md))

    uri         = "wc" ":" topic [ "@" version ][ "?" parameters ]
    topic       = STRING
    version     = 1*DIGIT
    parameters  = parameter *( "&" parameter )
    parameter   = key "=" value
    key         = STRING
    value       = STRING

### Parameters
#### Required
- `symKey` (STRING) = symmetric key used for pairing encryption
- `relay-protocol` (STRING) = protocol name used for relay
#### Optional
- `relay-data` (STRING) = hex data payload used for relay 

### Example
    topic = “7f6e504bfad60b485450578e05678ed3e8e8c4751d3c6160be17160d63ec90f9”
    version = 2
    symKey = “587d5484ce2a2a6ee3ba1962fdd7e8588e06200c46823bd18fbd67def96ad303”
    relay = { protocol: “waku”, data: “” }

```
uri = “wc:7f6e504bfad60b485450578e05678ed3e8e8c4751d3c6160be17160d63ec90f9@2?relay-protocol=waku&symKey=587d5484ce2a2a6ee3ba1962fdd7e8588e06200c46823bd18fbd67def96ad303”
```
