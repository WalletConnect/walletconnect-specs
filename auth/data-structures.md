# Data Structures

## Request Params

```typescript
interface RequestParams {
  chainId: string;
  nonce: string;
  aud?: string;
  nbf?: string;
  exp?: string;
  statement?: string;
  requestId?: string;
  resources?: string[];
}
```

## Respond Params

```typescript
interface RespondParams {
  topic: string;
  signature: CacaoSignature;
}
```

## Payload Params (partial Cacao)

Used for requester to authenticate wallet

```typescript
interface AuthPayloadParams {
  type: string; // same as Cacao Header type (t)
  chainId: string;
  domain: string;
  aud: string;
  version: string;
  nonce: string;
  iat: string;
  nbf?: string;
  exp?: string;
  statement?: string;
  requestId?: string;
  resources?: string[];
}
```

## Cacao Header (CAIP-70)

```typescript
interface CacaoHeader {
  t: string;
}
```

## Cacao Payload (CAIP-70)

```typescript
interface CacaoPayload {
  iss: string;
  domain: string;
  aud: string;
  version: string;
  nonce: string;
  iat: string;
  nbf?: string;
  exp?: string;
  statement?: string;
  requestId?: string;
  resources?: string[];
}
```

## Cacao Signature (CAIP-70)

```typescript
interface CacaoSignature {
  t: string;
  s: string;
  m?: string;
}
```

## Cacao (CAIP-70)

```typescript
interface Cacao {
  header: CacaoHeader;
  payload: CacaoPayload;
  signature: CacaoSignature;
}
```
