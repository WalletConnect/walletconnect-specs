
# Relay Server

## Purpose

This document aims to create the JsonRpc contract between a client and a server. 

## Definition

The message policy is a set of actions that have to be taken by involved parties without decrypting a message.<br>
One can create a new policy by providing an unique set of parameters and assigning an unique id to it.<br>
The number of policies is unlimited. Any policy is protocol agnostic.

## Parameters

The message policy is defined by the list of parameters, which describe the way of handling a given message. 

* **TTL** - defines a message storage duration on server-side in **seconds**. **(0 - forever, -1 - no caching)**
* **User prompt** - a flag that says whether a server should send a push to a client **(true/false)**
* **Api indicator** - a label that says what api sent a message. It allows collecting metrics. **(0 - sign, 1 - chat, 2 - auth, 3 - push)**

## Policies

| Policy id 	|   TTL   	|
|:------:	|:-------:	|
|    1   	|   5 min  	|
|    2   	|    6 h   	|
|    3   	|  7 days  	|
|    4   	| forever 	|


## Publish payload

Used when a client publishes a message to a server.

```jsonc
{
	"id" : "1",
	"jsonrpc": "2.0",
	"method": "irn_publish",
	"params" : {
		"topic" : string,
		"message" : string,  
        "policy" : number,
        "api" : number, 
        "prompt" : boolean
    }
}
```

## Subcribe payload

Used when a client subscribes a given topic.

```jsonc
{
	"id" : "1",
	"jsonrpc": "2.0",
	"method": "irn_subscribe",
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
	"method": "irn_unsubscribe",
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
	"method": "irn_subscription",
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

* What is a client? - Any SDK instance (Sign, Chat, Auth, Push)
* Might an sdk have more than one message policy? - Yes, there is such a possibility.