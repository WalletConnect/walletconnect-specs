# Client API

```typescript
abstract class Client {
  // ---------- Methods ----------------------------------------------- //

  // initializes the client with persisted storage and a network connection
  public abstract init(): Promise<void>;

  // for responder to pair a pairing created by a proposer
  public abstract pair(params: {
    uri: string;
  }): Promise<Sequence>;

  // request wallet authentication
  public abstract request(params: RequestParams): Promise<boolean>;

  // respond wallet authentication
  public abstract respond(params: RespondParams): Promise<boolean>;

  // for wallet to listen on auth request
  public abstract on("auth_request", (topic: string, payloadParams: PayloadParams) => {}): void;
  }
```
