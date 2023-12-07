# Authentication

In this document we will describe the authentication payloads for all [RPC methods](./rpc-methods.md).

All of the authentication payloads are DID JWTs and share the following claims:

- act - description of action intent. Must be equal to specific value defined in each payload
- iat - timestamp when JWT was issued
- exp - timestamp when JWT must expire. Must be equal to specific TTL defined in each payload, plus the iat value
- iss - did:key. Value defined specifically in each payload
- sub - did:pkh of blockchain account that this request is associated with
  - Example: `did:pkh:eip155:1:0x1234...`

Depending on the message, different keys are used for `iss`:
- "iss - did:key of client identity key" indicates JWTs sent by clients and are verified by the Notify Server with [Identity Keys](../core/identity/identity-keys.md).
- "iss - did:key of dapp authentication key" indicates JWTs sent by the Notify Server and are verified by clients with authentication key from [Dapp Authentication](./dapp-authentication.md).
- "iss - did:key of Notify Server authentication key" indicates JWTs sent by the Notify Server and are verified by clients with authentication key from [Notify Server Authentication](./notify-server-authentication.md).

## JWT TTLs & Expired JWT handling

JWT TTLs must be equal to the message TTLs they are sent with as defined in [RPC Methods](./rpc-methods.md).

If a client receives a JWT that is expired, it must ignore it as if it was never received. This is to ensure there are no race conditions between message TTL and JWT TTL.

A non-ideal way to avoid the race condition is for the sender to set the message TTL to slightly less than the JWT TTL, however this must not be done as message TTL is a relative time from when the relay receives the message and not absolute like the JWT expiration is.

## wc_notifyWatchSubscriptions request

- act - `notify_watch_subscriptions`
- iss - did:key of client identity key
- ksu - key server for identity key verification
- aud - did:key of Notify Server authentication key
- app - did:web of app domain to watch, or `null` to watch all domains
  - Example: `did:web:app.example.com`

## wc_notifyWatchSubscriptions response

- act - `notify_watch_subscriptions_response`
- iss - did:key of Notify Server authentication key
- aud - did:key of client identity key
- sbs - array of [Notify Server Subscriptions](./data-structures.md#notify-server-subscriptions)

## wc_notifySubscriptionsChanged request

- act - `notify_subscriptions_changed`
- iss - did:key of Notify Server authentication key
- aud - did:key of client identity key
- sbs - array of [Notify Server Subscriptions](./data-structures.md#notify-server-subscriptions)

## wc_notifySubscriptionsChanged response

- act - `notify_subscriptions_changed_response`
- iss - did:key of client identity key
- ksu - key server for identity key verification
- aud - did:key of Notify Server authentication key

## wc_notifySubscribe request

- act - `notify_subscription`
- iss - did:key of client identity key
- ksu - key server for identity key verification
- aud - did:key of dapp authentication key
- scp - space-delimited scope of notification types authorized by the user e.g. `promotional alerts`
- app - did:web of app domain that this request is associated with 
  - Example: `did:web:app.example.com`

## wc_notifySubscribe response

- act - `notify_subscription_response`
- iss - did:key of dapp authentication key
- aud - did:key of client identity key
- app - did:web of app domain that this request is associated with 
  - Example: `did:web:app.example.com`

## wc_notifyMessage request

- act - `notify_message`
- iss - did:key of dapp authentication key
- app - did:web of app domain that this request is associated with 
  - Example: `did:web:app.example.com`
- msg - [Notify Notification](./data-structures.md#notify-notification)

## wc_notifyMessage response

- act - `notify_message_response`
- iss - did:key of client identity key
- ksu - key server for identity key verification
- aud - did:key of dapp authentication key
- app - did:web of app domain that this request is associated with 
  - Example: `did:web:app.example.com`

## wc_notifyUpdate request

- act - `notify_update`
- iss - did:key of client identity key
- ksu - key server for identity key verification
- aud - did:key of dapp authentication key
- app - did:web of app domain that this request is associated with 
  - Example: `did:web:app.example.com`
- scp - space-delimited scope of notification types authorized by the user e.g. `promotional alerts`

## wc_notifyUpdate response

- act - `notify_update_response`
- iss - did:key of dapp authentication key
- aud - did:key of client identity key
- app - did:web of app domain that this request is associated with 
  - Example: `did:web:app.example.com`

## wc_notifyDelete request

- act - `notify_delete`
- iss - did:key of client identity key
- ksu - key server for identity key verification
- aud - did:key of dapp authentication key
- app - did:web of app domain that this request is associated with 
  - Example: `did:web:app.example.com`

## wc_notifyDelete response

- act - `notify_delete_response`
- iss - did:key of dapp authentication key
- aud - did:key of client identity key
- app - did:web of app domain that this request is associated with 
  - Example: `did:web:app.example.com`

## wc_notifyGetNotifications request

Paginated list of notifications with the most recent first.

- act - `notify_get_notifications`
- iss - did:key of client identity key
- ksu - key server for identity key verification
- aud - did:key of dapp authentication key
- app - did:web of app domain that this request is associated with 
  - Example: `did:web:app.example.com`
- lmt - the max number of notifications to return. Default value is 10, maximum value is 100.
- aft - the notification ID to start returning messages after. Null to start with the most recent notification

## wc_notifyGetNotifications response

- act - `notify_get_notifications_response`
- iss - did:key of client identity key
- aud - did:key of Notify Server authentication key
- nfs - array of [Notify Notifications](./data-structures.md#notify-notification)
- mre - true if there are more notifications, false otherwise

## wc_notifyGetNotification request

Paginated list of notifications with the most recent first.

- act - `notify_get_notification`
- iss - did:key of client identity key
- ksu - key server for identity key verification
- aud - did:key of dapp authentication key
- app - did:web of app domain that this request is associated with 
  - Example: `did:web:app.example.com`
- id - the notification ID to retrieve

## wc_notifyGetNotification response

- act - `notify_get_notification_response`
- iss - did:key of client identity key
- aud - did:key of Notify Server authentication key
- nfn - [Notify Notifications](./data-structures.md#notify-notification)

## wc_notifyNotificationChanged request

- act - `notify_notification_changed`
- iss - did:key of client identity key
- aud - did:key of Notify Server authentication key
- nfn - array of [Notify Notification](./data-structures.md#notify-notification)

## wc_notifyNotificationChanged response

- act - `notify_notification_changed_response`
- iss - did:key of client identity key
- ksu - key server for identity key verification
- aud - did:key of dapp authentication key
- aud - did:key of Notify Server authentication key

## wc_notifyReadNotification request

Paginated list of notifications with the most recent first.

- act - `notify_read_notification`
- iss - did:key of client identity key
- ksu - key server for identity key verification
- aud - did:key of dapp authentication key
- app - did:web of app domain that this request is associated with 
  - Example: `did:web:app.example.com`
- id - the notification ID to retrieve

## wc_notifyReadNotification response

- act - `notify_read_notification_response`
- iss - did:key of client identity key
- aud - did:key of Notify Server authentication key
