import Table from '../../../components/Table';

# Session Authenticate Events

## Events

You can set up event listeners to perform an action when these events are emitted.

<Table 
headers={[ "Event", "Description", "Who Should Listen" ]}
data={[
{
    event: "session_authenticate",
    description: "Emitted by wallet when there is a request for authentication from a dapp.",
    whoShouldListen: "Wallets"
  }
]}
/>

### session_authenticate

When the wallet receives an authorization request, it will emit the `session_authenticate` event. The wallet should listen for this event and respond by either accepting or rejecting the request.

An example of a payload from `session_authenticate`:

```ts
{
   "topic":"789ec778f85efc13f42e8a549b94c02e22c99113ba1a95c4805c3c3ae1ad2a7d",
   "params":{
      "authPayload":{
         "type":"caip122",
         "chains":[
            "eip155:1",
            "eip155:2"
         ],
         "aud":"aud",
         "version":"1",
         "domain":"https://walletconnect.com",
         "nonce":"1",
         "iat":"2023-12-18T15:31:28.071Z",
         "resources":[
            "urn:recap:eyJhdHQiOnsiZWlwMTU1IjpbeyJyZXF1ZXN0L3BlcnNvbmFsX3NpZ24iOltdfSx7InJlcXVlc3QvZXRoX3NpZ25UeXBlZERhdGFfdjQiOltdfV19fQ=="
         ]
      },
      "requester":{
         "publicKey":"49006eb041860891a010ce23ba981b095499271d0df80a3a4b53f936fb57773e",
         "metadata":{
            "name":"App A (Proposer)",
            "description":"Description of Proposer App run by client A",
            "url":"https://walletconnect.com",
            "icons":[
               "https://avatars.githubusercontent.com/u/37784886"
            ]
         }
      }
   },
   "id":1702913488072348
}
```

#### Wallet Approved Request

```ts
{
    "id": 1675889041848803,
    "topic": "4837ed5fd3ee6d0353c792b913f53548a1dff7d60760698c03eb96080ad59106",
    "params": {
        "id": 1675889041848803,
        "jsonrpc": "2.0",
        "result": {
            "cacaos": [
                {
                    "h": {
                        "t": "caip122"
                    },
                    "p": {
                        "type": "caip122",
                        "chains": [
                            "eip155:1",
                            "eip155:2"
                        ],
                        "aud": "aud",
                        "domain": "localhost",
                        "version": "1",
                        "nonce": "1",
                        "iat": "2023-12-18T15:35:00.148Z",
                        "resources": [
                            "urn:recap:eyJhdHQiOnsiZWlwMTU1IjpbeyJyZXF1ZXN0L3BlcnNvbmFsX3NpZ24iOltdfSx7InJlcXVlc3QvZXRoX3NpZ25UeXBlZERhdGFfdjQiOltdfV19fQ=="
                        ],
                        "chainId": "eip155:1",
                        "iss": "did:pkh:eip155:1:0x73bA24E3AA21546E0F4bC87d10bEE3d8bd637C79"
                    },
                    "s": {
                        "t": "eip191",
                        "s": "0x224d96875592f697dc36217b205cb53eb6295695cfe6893d15910513fe527e123534580ba5cbaa702f75ffee624130e3962bf64922504878ac57a97b34abd1431c"
                    }
                },
            ],
            "responder": {
                "publicKey": "ad35ae2b36554495d7a2928e16bdbc4614e61d470a2a42532ae91945562e730e",
                "metadata": {
                    "name": "App B (Responder)",
                    "description": "Description of Responder App run by client B",
                    "url": "https://app.b.walletconnect.com",
                    "icons": [
                        "https://avatars.githubusercontent.com/u/37784886"
                    ],
                    "redirect": {
                        "universal": "App B (Responder)",
                        "native": "App B Native (Responder)"
                    }
                }
            }
        }
    }
}
```

#### Wallet Rejects Request

```ts
{
    "id": 1675889292613258,
    "topic": "ba49b21e73c4b6875c63911a545c71743b589c51389f92e0daed0c3ae815b8ab",
    "params": {
        "id": 1675889292613258,
        "jsonrpc": "2.0",
        "error": {
            "message": "User rejected.",
            "code": 5000
        }
    }
}
```
