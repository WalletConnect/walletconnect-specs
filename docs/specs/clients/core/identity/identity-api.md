# Identity API


```typescript
abstract class IdentityClient {
  // Registers a blockchain account with an identity key if not yet registered on this client
  // onSign(message) promise for signing CAIP-122 message to verify blockchain account ownership
  // returns the public identity key. Method should throw if any errors comes from onSign promise. 
  public abstract register(params: {
    account: string;
    private?: boolean;
    domain: string,
    statement: string,
    onSign: (message: string) => Promise<Cacao.Signature>
  }): Promise<string>;

  // Unregisters a blockchain account with previously registered identity key 
  public abstract unregister(params: {
    account: string;
  }): Promise<void>;
```
