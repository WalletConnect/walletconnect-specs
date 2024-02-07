# Client SDK API

:::caution

Notify API is under development. Want early access? Join our [Pilot Program](https://walletconnect.com/partners)

:::

```typescript
abstract class Client {
  // ---------- Methods ----------------------------------------------- //

  // initializes the client with persisted storage and a network connection
  // Calls `wc_notifyWatchSubscriptions` for all previously registered accounts,
  // watches for future subscription updates, and subscribes to all notification topics
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

  // Get a paginated list of notifications with the most recently sent first.
  public abstract getNotificationHistory(params: {
    topic: string,
    // Default 10, max 50
    limit?: number,
    startingAfter?: string,
    // Default false
    unreadFirst?: string,
  }): Promise<{
    notifications: NotifyNotificationRecord[],
    hasMore: boolean,
    hasMoreUnread: boolean,
  }>

  // mark notification as read
  // returns true if operation was a success. 
  // For now, `read` can only be true. 
  public abstract updateNotificationsReadState(params: {
    topic: string,
    notificationIds: string[],
	read?: true
  }): Promise<boolean>

  // delete active subscription
  public abstract deleteSubscription(params: {
    topic: string,
  }): Promise<void>;

  // decrypt notify subscription message
  public abstract decryptNotification(params: {
    topic: string,
    encryptedMessage: string,
  }): Promise<NotifyNotification>;

  // Generates and returns SIWE message along with
  // Cacao Payload with identity key
  public abstract prepareRegistration(params: {
    account: string;
    domain: string,
    // Defaulted to false if not passed
    allApps?: boolean,
  }): Promise<{
    registerParams: NotifyRegistrationParams,
    message: string
  }>;

  // "Logs in" this client to notifications for the specified account. This involves:
  // - Verifies signature by reconstructing formatted message using provided payload
  // - Expected payload comes from prepareRegistration
  // - Once verified, registers the identity key on the keyserver
  // - Calls `wc_notifyWatchSubscriptions`, watches for future subscription updates, and subscribes to all notification topics
  // Returns the Identity Key public key. 
  public abstract register(params: {
    registerParams: NotifyRegistrationParams,
    signature: string;
  }): Promise<string>;

  // Returns true if account has a valid and up to date registration, false otherwise
  public abstract isRegistered(params: {
    account: string,
    // Defaulted to false if not passed
    allApps?: boolean,
    domain: string
  }): boolean

  // "Logs out" this client from any notifications for the specified account. This involves:
  // - Stops periodically calling `wc_notifyWatchSubscriptions` and unsubscribes from all related relay topics
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

  // for wallet to listen on notify notification
  public abstract on("notify_notification", (notification: NotifyNotificationRecord, metadata: Metadata) => {}): void;

  // Listen for when an existing notification has been changed
  public abstract on("notify_notifications_changed", (notifications: Record<string, { read: boolean }>) => {}): void;

  // for wallet to listen for result of notify subscription update
  public abstract on("notify_update", (result: NotifySubscription | Error) => {}): void;

  // for wallet to listen on notify deletion
  public abstract on("notify_delete", (topic: string) => {}): void;
}
```
