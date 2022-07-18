# Client API

```typescript
abstract class Client {
  // ---------- Methods ----------------------------------------------- //

  // initializes the client with persisted storage and a network connection
  public abstract init(): Promise<void>;

  // authenticate wallet users with blockchain account for provided chainId
  public abstract authenticate(params: {
    chainId: string;
    nonce: string;
    aud?: string;
    nbf?: string;
    exp?: string;
    statement?: string;
    requestId?: string;
    resources?: string[];
  }): Promise<boolean>;
}
```
