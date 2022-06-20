# Client Auth

## Motivation

WalletConnect supports e2e encrypted messaging between multiple clients.
Should a recipient be offline, WalletConnect stores the encrypted messages in the recipient's mailbox until they reconnect.
The address of the mailbox is a unique sticky _client id_ that clients present when they connect to WalletConnect.

This document discusses the creation and usage of the _client id_.

## Overview

WalletConnect expects an `Authentication Bearer <signed nonce>` header when establishing the Websocket connection.
WalletConnect exposes a `HTTP GET /auth-nonce?<client id>` endpoint to retrieve a nonce.

The `client id` is the public key of a public/private key pair that clients genererate when instantiating the SDK and keep for the entire lifecycle.

The Bearer is a standard JWT token.

## Key Pair and JWT Creation

### Test Cases

```JavaScript
// Client will sign the Server assigned socketId as a nonce
const nonce = 'c479fe5dc464e771e78b193d239a65b58d278cad1c34bfb0b5716e5bb514928e';

// Fixed seed to generate the same key pair
const seed = fromString('58e0254c211b858ef7896b00e3f36beeb13d568d47c6031c4218b87718061295','base16');

// Generate key pair from seed
const keyPair = ed25519.generateKeyPairFromSeed(seed);

// Expected JWT for given nonce
const expected = "eyJhbGciOiJFZERTQSIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJkaWQ6a2V5Okh4Z05FWFdCUmtOemYycXZONXl2Z01oTWI2eXJGZE5INkpwSENaYzMxR3k4eDFHbSIsInN1YiI6ImM0NzlmZTVkYzQ2NGU3NzFlNzhiMTkzZDIzOWE2NWI1OGQyNzhjYWQxYzM0YmZiMGI1NzE2ZTViYjUxNDkyOGUifQ.uHUKtFTEkKwbdWKDM8PlYv1Xl8yj2Fr9wSPyICSS5HjXTfkG2RzQuM5m1hi9nwa1vbyFP88vupB23YB3NfMuBA"

async function test() {
  const jwt = await signJWT(nonce, keyPair)
  console.log('jwt', jwt)
  console.log('matches', jwt === expected)
  const verified = await verifyJWT(jwt)
  console.log('verified', verified)
}

test()
```

### API

```JavaScript
import * as ed25519 from '@stablelib/ed25519';
import { concat } from 'uint8arrays/concat';
import { toString } from 'uint8arrays/to-string';
import { fromString } from 'uint8arrays/from-string';
import { safeJsonParse, safeJsonStringify } from "@walletconnect/safe-json";

// ---------- Interfaces ----------------------------------------------- //

interface IridiumJWTHeader {
  alg: "EdDSA";
  typ: "JWT";
}

interface IridiumJWTPayload {
  iss: string;
  sub: string;
}

interface IridiumJWTParams {
  header: IridiumJWTHeader;
  payload: IridiumJWTPayload;
}

interface IridiumJWTSigned extends IridiumJWTParams {
  signature: Uint8Array;
}

// ---------- Constants ----------------------------------------------- //

const JWT_IRIDIUM_ALG: IridiumJWTHeader["alg"] = "EdDSA"

const JWT_IRIDIUM_TYP: IridiumJWTHeader["typ"] = "JWT"

const JWT_DELIMITER = "."

const JWT_ENCODING = 'base64url'

const JSON_ENCODING = 'utf8'

const DID_DELIMITER = ":"

const DID_METHOD_PREFIX = "did:key"

const MULTICODEC_ED25519_ENCODING = 'base58btc'

const MULTICODEC_ED25519_HEADER = 'z6Mk'

const MULTICODEC_ED25519_LENGTH = 32

// ---------- Utilities ----------------------------------------------- //

function decodeJSON(str: string): any {
  return safeJsonParse(toString(fromString(str, JWT_ENCODING), JSON_ENCODING))
}

function encodeJSON(val: any): string {
  return toString(fromString(safeJsonStringify(val), JSON_ENCODING), JWT_ENCODING)
}

function encodeIss(publicKey: Uint8Array): string {
  const multicodec = concat([fromString(MULTICODEC_ED25519_HEADER, MULTICODEC_ED25519_ENCODING), publicKey])
  return `${DID_METHOD_PREFIX}:${toString(multicodec, MULTICODEC_ED25519_ENCODING)}`
}

function decodeIss(issuer: string): Uint8Array {
  if (!issuer.startsWith(DID_METHOD_PREFIX)) {
    throw new Error(`Issuer must be a DID with method "key"`)
  }
  const params = issuer.split(DID_DELIMITER)
  const multicodec = fromString(params[2], MULTICODEC_ED25519_ENCODING)
  const header = multicodec.slice(0,3)
  if (toString(header, MULTICODEC_ED25519_ENCODING) !== MULTICODEC_ED25519_HEADER) {
    throw new Error(`Issuer must be a public key with type "Ed25519"`)
  }
  const publicKey = multicodec.slice(3, 35)
  if (publicKey.length !== MULTICODEC_ED25519_LENGTH) {
    throw new Error(`Issuer must be a public key with length 32 bytes`)
  }
  return publicKey
}

function encodeSig(bytes: Uint8Array): string {
  return toString(bytes, JWT_ENCODING)
}

function decodeSig(encoded: string): Uint8Array {
  return fromString(encoded, JWT_ENCODING)
}

function encodeParams(params: IridiumJWTParams): string {
  return [
    encodeJSON(params.header),
    encodeJSON(params.payload)
  ].join(JWT_DELIMITER)
}

function decodeParams(jwt: string): IridiumJWTParams {
  const params = jwt.split(JWT_DELIMITER)
  const header = decodeJSON(params[0])
  const payload = decodeJSON(params[1])
  return { header, payload }
}

function encodeJWT(params: IridiumJWTSigned): string {
  return [
    encodeJSON(params.header),
    encodeJSON(params.payload),
    encodeSig(params.signature)
  ].join(JWT_DELIMITER)
}

function decodeJWT(jwt: string): IridiumJWTSigned {
  const params = jwt.split(JWT_DELIMITER)
  const header = decodeJSON(params[0])
  const payload = decodeJSON(params[1])
  const signature = decodeSig(params[2])
  return { header, payload, signature }
}

// ---------- API ----------------------------------------------- //

async function signJWT(subject: string, keyPair: ed25519.KeyPair) {
  const header = { "alg": JWT_IRIDIUM_ALG, "typ": JWT_IRIDIUM_TYP }
  const issuer = encodeIss(keyPair.publicKey)
  const payload = { "iss": issuer, "sub": subject }
  const dataToSign = fromString(encodeParams({ header, payload }), "utf8")
  const signature = ed25519.sign(keyPair.secretKey, dataToSign)
  return encodeJWT({ header, payload, signature })
}

async function verifyJWT(jwt: string) {
  const { header, payload, signature } = decodeJWT(jwt)
  if (header.alg !== JWT_IRIDIUM_ALG || header.typ !== JWT_IRIDIUM_TYP) {
    throw new Error('JWT must use EdDSA algorithm')
  }
  const publicKey = decodeIss(payload.iss)
  const dataToSign = fromString(encodeParams({ header, payload }), "utf8")
  return ed25519.verify(publicKey, dataToSign, signature)
}
```