# Authentication

In this document we will describe the authentication payloads for all [RPC methods](./rpc-methods.md).

All of the authentication payloads are DID JWTs and share the following claims:

- act - description of action intent. Must be equal to specific value defined in each payload
- iat - timestamp when JWT was issued
- exp - timestamp when JWT must expire. Must be equal to specific TTL defined in each payload, plus the iat value

"iss - did:key of client identity key" indicates JWTs sent by clients and are verified by the Notify Server with [Identity Keys](../core/identity/identity-keys.md).

"iss - did:key of dapp authentication key" indicates JWTs sent by the Notify Server and are verified by clients with authentication key from [Dapp Authentication](./dapp-authentication.md).

"iss - did:key of Notify Server authentication key" indicates JWTs sent by the Notify Server and are verified by clients with authentication key from [Notify Server Authentication](./notify-server-authentication.md).

## JWT TTLs & Expired JWT handling

JWT TTLs must be equal to the message TTLs they are sent with as defined in [RPC Methods](./rpc-methods.md).

If a client receives a JWT that is expired, it must ignore it as if it was never received. This is to ensure there are no race conditions between message TTL and JWT TTL.

A non-ideal way to avoid the race condition is for the sender to set the message TTL to slightly less than the JWT TTL, however this must not be done as message TTL is a relative time from when the relay receives the message and not absolute like the JWT expiration is.

## wc_notifyWatchSubscriptions request

- act - description of action intent. Must be equal to "notify_watch_subscriptions"
- iss - did:key of client identity key
- ksu - key server for identity key verification
- aud - did:key of Notify Server authentication key
- sub - did:pkh of blockchain account that this request is associated with

## wc_notifyWatchSubscriptions response

- act - description of action intent. Must be equal to "notify_watch_subscriptions_response"
- iss - did:key of Notify Server authentication key
- aud - did:key of client identity key
- sub - did:pkh of blockchain account that this request is associated with
- sbs - array of [Notify Server Subscriptions](./data-structures.md#notify-server-subscriptions)

## wc_notifySubscriptionsChanged request

- act - description of action intent. Must be equal to "notify_subscriptions_changed"
- iss - did:key of Notify Server authentication key
- aud - did:key of client identity key
- sub - did:pkh of blockchain account that this request is associated with
- sbs - array of [Notify Server Subscriptions](./data-structures.md#notify-server-subscriptions)

## wc_notifySubscriptionsChanged response

- act - description of action intent. Must be equal to "notify_subscriptions_changed_response"
- iss - did:key of client identity key
- ksu - key server for identity key verification
- aud - did:key of Notify Server authentication key
- sub - did:pkh of blockchain account that this request is associated with

## wc_notifySubscribe request

- act - description of action intent. Must be equal to "notify_subscription"
- iss - did:key of client identity key
- ksu - key server for identity key verification
- aud - did:key of dapp authentication key
- sub - did:pkh of blockchain account that this notify subscription is associated with
- scp - scope of notification types authorized by the user
- app - dapp's domain URL

## wc_notifySubscribe response

- act - description of action intent. Must be equal to "notify_subscription_response"
- iss - did:key of dapp authentication key
- aud - did:key of client identity key
- sub - did:key of the public key used for key agreement on the Notify topic 
- app - dapp's domain URL

## wc_notifyMessage request

- act - description of action intent. Must be equal to "notify_message"
- iss - did:key of dapp authentication key
- aud - did:pkh of blockchain account that notify subscription is associated with
- sub - hash of the matching subscription payload
- app - dapp's domain URL
- msg - message object including the following parameters:
    - title - short message used in the title of the notification
    - body - long messages ued in the body of the notification
    - icon - image URL used to display with the notification
    - URL -  redirect URL for call-to-action related to notification
    - type - notification type which matches the scope of notify subscription

## wc_notifyMessage response

- act - description of action intent. Must be equal to "notify_message_response"
- iss - did:key of client identity key
- ksu - key server for identity key verification
- aud - did:key of dapp authentication key
- sub - hash of the stringified notify message object received
- app - dapp's domain URL

## wc_notifyUpdate request

- act - description of action intent. Must be equal to "notify_update"
- iss - did:key of client identity key
- ksu - key server for identity key verification
- aud - did:key of dapp authentication key
- sub - did:pkh of blockchain account that this notify subscription is associated with
- scp - scope of notification types authorized by the user
- app - dapp's domain URL

## wc_notifyUpdate response

- act - description of action intent. Must be equal to "notify_update_response"
- iss - did:key of dapp authentication key
- aud - did:key of client identity key
- sub - hash of the new subscription payload
- app - dapp's domain URL

## wc_notifyDelete request

- act - description of action intent. Must be equal to "notify_delete"
- iss - did:key of client identity key
- ksu - key server for identity key verification
- aud - did:key of dapp authentication key
- sub - reason for deleting the subscription
- app - dapp's domain URL

## wc_notifyDelete response

- act - description of action intent. Must be equal to "notify_delete_response"
- iss - did:key of dapp authentication key
- aud - did:key of client identity key
- sub - hash of the existing subscription payload
- app - dapp's domain URL
