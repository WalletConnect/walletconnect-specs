# Dapp Authentication

Notify API requires Dapp developers to host a did:web document to expose public keys for two specific purposes:

- key agreement - public key used to derive the symetric key and topic for communication with the notify server
- authentication - public key used to authenticate messages published by the notify server

This should be available as a `did.json` document under the `.well-known` path for the Dapp Domain specified as did:web identifier.

Here is an example for the two public keys being exposed:

```jsonc
did:web:app.example.com -> https://app.example.com/.well-known/did.json

// did.json
{
  "@context": [
    "https://www.w3.org/ns/did/v1",
    "https://w3id.org/security/suites/jws-2020/v1"
  ],
  "id": "did:web:app.example.com",
  "verificationMethod": [
    {
      "id": "did:web:app.example.com#wc-notify-subscribe-key",
      "type": "JsonWebKey2020",
      "controller": "did:web:app.example.com",
      "publicKeyJwk": {
        "kty": "OKP",
        "crv": "X25519",
        "x": "9GXjPGGvmRq9F6Ng5dQQ_s31mfhxrcNZxRGONrmH30k"
      }
    }, {
      "id": "did:web:app.example.com#wc-notify-authentication-key",
      "type": "JsonWebKey2020",
      "controller": "did:web:app.example.com",
      "publicKeyJwk": {
        "kty": "OKP",
        "crv": "Ed25519",
        "x": "0-e2i2_Ua1S5HbTYnVB0lj2Z2ytXu2-tYmDFf8f5NjU"
      }
    },

  ],
  "keyAgreement": [
    "did:web:app.example.com#wc-notify-subscribe-key"
  ],
  "authentication": [
    "did:web:app.example.com#wc-notify-authentication-key"
  ],
}
```
