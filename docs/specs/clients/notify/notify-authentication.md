# Notify Authentication

In this document we will describe the authentication payloads for all [RPC methods](./rpc-methods.md).

All of the authentication payloads are DID JWTs and share the following claims:

- act - description of action intent. Must be equal to specific value defined in each payload
- iat - timestamp when JWT was issued
- exp - timestamp when JWT must expire. Must be equal to specific TTL defined in each payload, plus the iat value
- ksu - key server for identity key verification

"Signed by: client identity key" indicates payloads sent by clients and are authenticated by the Notify Server with [Identity Keys](../../servers/keys/identity-keys).

"Signed by: dapp identity key" indicates payloads sent by the Notify Server and are authenitcated by clients with [Dapp Authentication](./dapp-authentication.md).

"Signed by: Notify Server identity key" indicates payloads sent by the Notify Server and are authenitcated by clients with [Notify Server Authentication](./notify-server-authentication.md).

## JWT TTLs & Expired JWT handling

JWT TTLs must be equal to the message TTLs they are sent with as defined in [RPC Methods](./rpc-methods.md).

If a client receives a JWT that is expired, it must ignore it as if it was never received. This is to ensure there are no race conditions between message TTL and JWT TTL.

A non-ideal way to avoid the race condition is for the sender to set the message TTL to slightly less than the JWT TTL, however this must not be done as message TTL is a relative time from when the relay receives the message and not absolute like the JWT expiration is.

## wc_notifyWatchSubscriptions request

- act - description of action intent. Must be equal to "notify_watch_subscriptions"
- iss - did:key of an identity key. Enables to resolve attached blockchain account.
- aud - did:key of an identity key. Enables to resolve associated Dapp domain used.
- sub - blockchain account that this request is associated with (did:pkh)
- app - dapp's domain URL

Signed by: client identity key

## wc_notifyWatchSubscriptions response

- act - description of action intent. Must be equal to "notify_watch_subscriptions_response"
- iss - did:key of an identity key. Allows for the resolution of which Notify Server was used.
- aud - did:key of an identity key. Allows for the resolution of the attached blockchain account.
- sub - did:key of the public key used for key agreement on the Notify topic 
- app - dapp's domain URL
- subs - [Notify Server Subscriptions](./data-structures.md#notify-server-subscriptions)

Signed by: Notify Server identity 

## wc_notifySubscriptionsChanged request

- act - description of action intent. Must be equal to "notify_subscriptions_changed"
- iss - did:key of an identity key. Enables to resolve associated Notify Server used.
- aud - blockchain account that notify subscription is associated with (did:pkh)
- sub - hash of the matching subscription payload
- app - dapp's domain URL
- subs - [Notify Server Subscriptions](./data-structures.md#notify-server-subscriptions)

Signed by: Notify Server identity key

## wc_notifySubscriptionsChanged response

- act - description of action intent. Must be equal to "notify_receipt"
- iss - did:key of an identity key. Enables to resolve attached blockchain account.
- aud - did:key of an identity key. Enables to resolve associated Dapp domain used.
- sub - hash of the stringified notify message object received
- app - dapp's domain URL

Signed by: client identity key

## wc_notifySubscribe request

- act - description of action intent. Must be equal to "notify_subscription"
- iss - did:key of an identity key. Enables to resolve attached blockchain account.
- aud - did:key of an identity key. Enables to resolve associated Dapp domain used.
- sub - blockchain account that this notify subscription is associated with (did:pkh)
- scp - scope of notification types authorized by the user
- app - dapp's domain URL

Signed by: client identity key

## wc_notifySubscribe response

- act - description of action intent. Must be equal to "notify_subscription_response"
- iss - did:key of an identity key. Allows for the resolution of which Notify Server was used.
- aud - did:key of an identity key. Allows for the resolution of the attached blockchain account.
- sub - did:key of the public key used for key agreement on the Notify topic 
- app - dapp's domain URL

Signed by: dapp identity key

## wc_notifyMessage request

- act - description of action intent. Must be equal to "notify_message"
- iss - did:key of an identity key. Enables to resolve associated Dapp domain used.
- aud - blockchain account that notify subscription is associated with (did:pkh)
- sub - hash of the matching subscription payload
- app - dapp's domain URL
- msg - message object including the following parameters:
    - title - short message used in the title of the notification
    - body - long messages ued in the body of the notification
    - icon - image URL used to display with the notification
    - URL -  redirect URL for call-to-action related to notification
    - type - notification type which matches the scope of notify subscription

Signed by: dapp identity key

## wc_notifyMessage response

- act - description of action intent. Must be equal to "notify_receipt"
- iss - did:key of an identity key. Enables to resolve attached blockchain account.
- aud - did:key of an identity key. Enables to resolve associated Dapp domain used.
- sub - hash of the stringified notify message object received
- app - dapp's domain URL

Signed by: client identity key

## wc_notifyUpdate request

- act - description of action intent. Must be equal to "notify_update"
- iss - did:key of an identity key. Enables to resolve attached blockchain account.
- aud - did:key of an identity key. Enables to resolve associated Dapp domain used.
- sub - blockchain account that this notify subscription is associated with (did:pkh)
- scp - scope of notification types authorized by the user
- app - dapp's domain URL

Signed by: client identity key

## wc_notifyUpdate response

- act - description of action intent. Must be equal to "notify_update_response"
- iss - did:key of an identity key. Enables to resolve associated Dapp domain used.
- aud - did:key of an identity key. Enables to resolve attached blockchain account.
- sub - hash of the new subscription payload
- app - dapp's domain URL

Signed by: dapp identity key

## wc_notifyDelete request

- act - description of action intent. Must be equal to "notify_delete"
- iss - did:key of an identity key. Enables to resolve attached blockchain account.
- aud - did:key of an identity key. Enables to resolve associated Dapp domain used.
- sub - reason for deleting the subscription
- app - dapp's domain URL

Signed by: client identity key

## wc_notifyDelete response

- act - description of action intent. Must be equal to "notify_delete_response"
- iss - did:key of an identity key. Enables to resolve associated Dapp domain used.
- aud - did:key of an identity key. Enables to resolve attached blockchain account.
- sub - hash of the existing subscription payload
- app - dapp's domain URL

Signed by: dapp identity key
