# Verify Server API

## Generate Challenge (iOS only)

Generates a unique one-time challenge to be later used with other requests as a mean to prevent replay attacks.

### Request

`POST /challenge`

### Responses

- `201` - Challenge generated

```jsonc
{
    "id": string
}
```

## Register Attestor (iOS only)

Registers an instance of iOS dApp.

["Verify the Attestation"](https://developer.apple.com/documentation/devicecheck/validating_apps_that_connect_to_your_server#3576643) step from Apple docs.

### Request

`POST /attestor/:id`
```jsonc
{
    "attestationObject": [number] 
}
```
- `attestationObject` - byte array representing CBOR encoded ["Attestation Object"](https://developer.apple.com/documentation/devicecheck/validating_apps_that_connect_to_your_server#3576643)

### Responses

- `201` - Attestor registered
- `400` - Invalid request body or `attestationObject` format
- `401` - Failed to verify the `attestationObject` according to ["Verify the Attestation"](https://developer.apple.com/documentation/devicecheck/validating_apps_that_connect_to_your_server#3576643)
- `403` - App ID is not registered on [WalletConnect Cloud](https://cloud.walletconnect.com)

## Register Attestation

Registers dApp attestation.

### Web

Uses our custom iframe Enclave solution.

#### Request

`POST /attestation/:id`
```jsonc
{
    "origin": string
}
```
- `origin` - Web dApp origin 

#### Responses

- `201` - Attestation registered
- `400` - Invalid request body

### Android

Uses [Google Play Integrity API](https://developer.android.com/google/play/integrity/overview).

["Decrypt and verify the integrity verdict"](https://developer.android.com/google/play/integrity/standard#decrypt-and) step from Android docs.

#### Request

`POST /attestation/:id`
```jsonc
{
    "integrityToken": string
}
```
- `integrityToken` - token provided to the Android dApp by [Integrity API](https://developer.android.com/google/play/integrity/standard#request-integrity)

#### Responses

- `201` - Attestation registered
- `400` - Invalid request body or `integrityToken` format 
- `401` - Failed to verify the `integrityToken` according to ["Decrypt and verify the integrity verdict"](https://developer.android.com/google/play/integrity/standard#decrypt-and)
- `403` - Bundle ID is not registered on [WalletConnect Cloud](https://cloud.walletconnect.com)

### iOS

Uses [Apple DeviceCheck](https://developer.apple.com/documentation/devicecheck/).

["Verify the Assertion"](https://developer.apple.com/documentation/devicecheck/validating_apps_that_connect_to_your_server#3576644) step from Apple docs.

#### Request

`POST /attestation/:id`
```jsonc
{   
    "keyId": string, 
    "assertion": [number]
}
```
- `keyId` - key identifier used when registering the Attestor
- `assertion` - byte array representing CBOR encoded ["Assertion Object"](https://developer.apple.com/documentation/devicecheck/validating_apps_that_connect_to_your_server#3576644)

#### Responses

- `201` - Attestation registered
- `400` - Invalid request body or `assertion` format
- `401` - Failed to authenticate the provided `keyId`

## Resolve Attestation

Resolves previously registered dApp attestation for a wallet. 

### Web

#### Request

`GET /attestation/:id`

#### Responses

- `200` - Attestation exists
    - `origin` - origin of the Web dApp that registered this attestation
    - `isScam` - indicator of whether this dApp is marked as a scam in our databases
        - nullable, `null` meaning we don't know
```jsonc
{
    "origin": string,
    "isScam": boolean
}
```
- `404` - Attestation doesn't exist

### Android

#### Request

`GET /attestation/:id`

#### Responses

- `200` - Attestation exists
    - `bundleId` - Bundle ID of the Android dApp that registered this attestation
```jsonc
{
    "bundleId": string
}
```
- `404` - Attestation doesn't exist

### iOS

#### Request

`GET /attestation/:id`

#### Responses

- `200` - Attestation exists
    - `appId` - App ID of the iOS dApp that registered this attestation
```jsonc
{
    "appId": string
}
```
