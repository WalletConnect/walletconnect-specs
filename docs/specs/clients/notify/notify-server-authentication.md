# Notify Server Authentication

Notify Server hosts a did:web document to expose public keys for two specific purposes:

- key agreement - public key used to derive the symetric key and topic for communication with the notify server
- authentication - public key used to authenticate messages published by the notify server

Here is an example for the two public keys being exposed:

```jsonc
did:web:notify.walletconnect.com -> https://notify.walletconnect.com/.well-known/did.json

// did.json
{
  "@context": [
    "https://www.w3.org/ns/did/v1",
    "https://w3id.org/security/suites/jws-2020/v1"
  ],
  "id": "did:web:notify.walletconnect.com",
  "verificationMethod": [
    {
      "id": "did:web:notify.walletconnect.com#key-0",
      "type": "JsonWebKey2020",
      "controller": "did:web:notify.walletconnect.com",
      "publicKeyJwk": {
        "kty": "OKP",
        "crv": "X25519",
        "x": "9GXjPGGvmRq9F6Ng5dQQ_s31mfhxrcNZxRGONrmH30k"
      }
    },
    {
      "id": "did:web:notify.walletconnect.com#key-1",
      "type": "JsonWebKey2020",
      "controller": "did:web:notify.walletconnect.com",
      "publicKeyJwk": {
        "kty": "OKP",
        "crv": "Ed25519",
        "x": "0-e2i2_Ua1S5HbTYnVB0lj2Z2ytXu2-tYmDFf8f5NjU"
      }
    },

  ],
  "keyAgreement": [
    "did:web:notify.walletconnect.com#key-0"
  ],
  "authentication": [
    "did:web:exnotify.walletconnectample.com#key-1"
  ],
}
```
