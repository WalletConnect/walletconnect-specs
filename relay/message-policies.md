
# Relay Message Policies

## Purpose

This document aims to create the message policy contracts between SDKs and the cloud. 

## Definition

The message policy is a set of actions that have to be taken by involved parties without decrypting a message.<br>
One can create a new policy by providing an unique set of parameters.<br>
The number of policies is unlimited. Any policy is protocol agnostic.

## Parameters

The message policy is defined by the list of parameters, which describe the way of handling a given message. 

* Storage duration - defines a message storage duration on server-side in **seconds**. **(0 - forever, -1 - no caching)**
* User prompt - a flag that says whether a server should send a push to a client **(true/false)**
* Api label - a label that says what api sent a message. It allows collecting metrics. **(sign, chat, auth, push)**

## Policies

| Policy 	| Api label<br> 	|                          User prompt                         	| Server-side<br>storage duration 	|
|:------:	|:-------------:	|:------------------------------------------------------------:	|:-------------------------------:	|
|    1   	|      sign     	| true<br>when<br>wc_sessionPropose<br>or<br>wc_sessionRequest 	|                6h               	|
|    2   	|      chat     	|                true<br>when<br>wc_chatMessage                	|                *                	|

\* not defined yet

## Publish payload

Publish payload constructs a data structure that is to send over the network. The policy for a message is defined in the policy object.

```jsonc
{
	"id" : "1",
	"jsonrpc": "2.0",
	"method": string,
	"params" : {
		"topic" : string,
		"message" : string,  
		"ttl" : Int64, //seconds

        "policy": {
            "indicator" : number,
            "api" : string, 
            "prompt" : boolean,
        }
    }
}
```

## Relay API

Relay API defines a public interface with set of supported methods.

```kotlin
interface Relay {

    /*Publishes a message over the network under given topic*/
    fun publish(topic: String, message: String, policy: Policy)

    /*Subcribes on topic to receive messages*/
    fun subscribe(topic: String)

    /*Unsubcribes from a topic*/
    fun unsubscribe(topic: String, subscriptionId: String)
	
    /*Opens a Web-Socket connection*/
    fun connect()

    /*Closes a Web-Socket connection*/
    fun disconnect()
}
```

### Policy

The policy object defines the policy's parameters.

```jsonc
{
    "indicator" : number,
    "api" : string,
    "prompt" : boolean
}
```

## FAQ

* What is a client? - Any SDK instance (Sign, Chat, Auth, Push)
* Why does an sdk instance decide on a message ttl? - A possibility to allow chat users to manage a message's life duration.
* Might an sdk have more than one message policy? - Perhaps. There is such possibility.