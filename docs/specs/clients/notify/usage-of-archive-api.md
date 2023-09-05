# Cold start of Notify SDK with Archive API

## Motivation

A cold start represents the Notify SDK running for the first time on a device without any cached state. The account that is used for `enableSync` of NotifyClient API may already have some kind of history (notifyMessages). The Archive API's purpose is to restore history and state after a cold start.

Note: Launching a client after 30 days of inactivity is also considered a cold start

## Implementation

1. W1 already has some `notifyMessages` history
2. Wn performs cold start on different device but the same account as W1
3. Wn configures sync for an account with `enableSync` method of NotifyClient
4. Wn registers Archive API for `wc_syncSet`, `wc_syncDel` of Sync API methods and `notify_message` of Notify API method. Request tags can be found in [Sync API](../core/sync/rpc-methods.md) and [Notify API](./rpc-methods.md) specs
5. Wn fetches `wc_notifyMessage` messages for every subscription topic from Archive API. Request tags can be found in [Notify RPC methods](./rpc-methods.md) 
6. Wn decrypts notifyMessages payload with symKey P and updates local database
