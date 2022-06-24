# Relay Server

## Purpose

This document aims to create the JsonRpc contract between a client and a server.

## Definitions

The message policy is a set of actions that have to be taken by involved parties without decrypting a message.
One can create a new policy by providing an unique TTL value.
The TTL value equals the message policy.
The number of policies is unlimited.
Any policy is protocol agnostic.

- **TTL** - defines a message storage duration on server-side in **seconds**. **(0 - no caching, -1 - forever)**
- **User prompt** - a flag that says whether a server should send a push to a client **(true/false)**
- **Api label** - a label that says what api sent a message. It allows collecting metrics. **(0 - unknown, 1 - sign, 2 - chat, 3 - auth, 4 - push)**

## Publish payload

Used when a client publishes a message to a server.

```jsonc
{
  "id" : "1",
  "jsonrpc": "2.0",
  "method": "iridium_publish",
  "params" : {
    "topic" : string,
    "message" : string,
    "ttl" : seconds,
    "prompt" : boolean,
    "api" : number,
  }
}
```

## Subcribe payload

Used when a client subscribes a given topic.

```jsonc
{
  "id" : "1",
  "jsonrpc": "2.0",
  "method": "iridium_subscribe",
  "params" : {
    "topic" : string
  }
}
```

## Unsubcribe payload

Used when a client unsubscribes a given topic.

```jsonc
{
  "id" : "1",
  "jsonrpc": "2.0",
  "method": "iridium_unsubscribe",
  "params" : {
    "topic" : string,
    "id": string
  }
}
```

## Subscription payload

Used when a server sends a subscription message to a client.

```jsonc
{
  "id" : "1",
  "jsonrpc": "2.0",
  "method": "iridium_subscription",
  "params" : {
    "id" : string,
    "data" : {
      "topic" : string,
      "message": string
    }
  }
}
```

## FAQ

- What is a client? - Any SDK instance (Sign, Chat, Auth, Push)
