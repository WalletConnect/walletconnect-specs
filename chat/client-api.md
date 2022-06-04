# Client API

```typescript
abstract class Client {
  // ---------- Methods ----------------------------------------------- //

  // initializes the client with persisted storage and a network connection
  public abstract init(): Promise<void>;

  // register a blockchain account with a public key and returns the public key
  public abstract register(params: {
    account: string;
  }): Promise<string>;

  // queries the default keyserver with a blockchain account and returns the public key
  public abstract resolve(params: {
    account: string;
  }): Promise<string>;

  // sends a chat invite with opening message and returns an invite id
  public abstract invite(params: {
    publicKey: string;
    openingMessage: string;
  }): Promise<number>;

  // sends a chat message to an active chat thread
  public abstract message(params: {
    topic: string;
    message: string;
  }): Promise<void>;

  // ping its peer to evaluate if it's currently online
  public abstract ping(params: {
    topic: string;
  }): Promise<void>

  // leaves a chat thread and stops receiving messages
  public abstract leave(params: {
    topic: string;
  }): Promise<void>;

  // ---------- Events ----------------------------------------------- //

  // subscribe to new chat invites received
  public abstract on("chat_invite", ({ id: number, openingMessage: string }) => {}): void;

  // subscribe to new chat thread joined
  public abstract on("chat_joined",  ({ topic: string }) => {}): void;

  // subscribe to new chat messages received
  public abstract on("chat_message", ({ topic: string, message: string }) => {}): void;

  // subscribe to new chat thread left
  public abstract on("chat_left",  ({ topic: string }) => {}): void;
}
```
