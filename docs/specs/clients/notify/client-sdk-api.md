# Client SDK API

:::caution

Notify API is under development. Want early access? Join our [Pilot Program](https://walletconnect.com/partners)

:::

```typescript
abstract class Client {
  // ---------- Methods ----------------------------------------------- //

  // initializes the client with persisted storage and a network connection
  public abstract init(): Promise<void>;
  
  // send notify subscription
  public abstract subscribe(params: { 
    appDomain: string,
    account: string,
  }): Promise<boolean>;

  // update notify subscription
  public abstract update(params: {
    topic: string,
    scope: string[],
  }): Promise<boolean>;

  // query notification types available for a dapp domain
  public abstract getNotificationTypes(params: {
    appDomain: string,
  }): Promise<NotifyAvailableTypes>

  // query all active subscriptions
  public abstract getActiveSubscriptions(params: {
    account: string,
  }): Promise<Record<string, NotifySubscription>>;

  // get all messages for a subscription
  public abstract getMessageHistory(params: {
    topic: string,
  }): Promise<Record<number, NotifyMessageRecord>>

  // delete active subscription
  public abstract deleteSubscription(params: {
    topic: string,
  }): Promise<void>;
  
  // delete notify message
  public abstract deleteNotifyMessage(params: {
    id: number,
  }): Promise<void>;
  
  // decrypt notify subscription message
  public abstract decryptMessage(params: {
    topic: string,
    encryptedMessage: string,
  }): Promise<NotifyMessage>;
  
  // exposed IdentityClient.register method
  // registers device in Push Server
  // returns the public identity key. Method should throw 'signatureRejected' if any errors comes from onSign promise. 
  public abstract register(params: {
    account: string;
    private?: boolean;
    domain: string,
    onSign: (message: string) => Promise<Cacao.Signature>
  }): Promise<string>;

  // "Logs out" this client from any notifications for the specified account. This involves:
  // - Stops periodically calling `wc_notifyWatchSubscriptions` and unsubscribes from all related relay topics
  // - Unregisters the client from Push Server
  // - Unregisters the Identity Key and removes it from local storage
  // This is not to be confused with `deleteSubscription()` method
  public abstract unregister(params: {
    account: string;
  }): Promise<Void>;


  // ---------- Events ----------------------------------------------- //

  // for updates from watch subscriptions
  public abstract on("notify_subscriptions_changed", (result: Record<string, NotifySubscription>) => {}): void;

  // for wallet to listen for notify subscription created
  public abstract on("notify_subscription", (result: NotifySubscription | Error) => {}): void;
  
  //  for wallet to listen on notify messages
  public abstract on("notify_message", (message: NotifyMessageRecord, metadata: Metadata) => {}): void;
  
  // for wallet to listen for result of notify subscription update
  public abstract on("notify_update", (result: NotifySubscription | Error) => {}): void;

  // for wallet to listen on notify deletion
  public abstract on("notify_delete", (topic: string) => {}): void;
}
```
