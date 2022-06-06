
# Relay message policies

## Purpose
The purpose of this document is to define the message policies on both client and server sides.

## Definition

The message policy is a set of rules/actions that have to be taken to a message by involved parties without decrypting the message. Those rules are restricted by the list of parameters shown below.

## Parameters

The message policy is defined by the list of parameters, which describe the way of handling a given message. 

* Publishing retrials - defines how many tries have to be taken when publishing a message fails
* Caching duration - defines a caching duration in **seconds**. **(0 - forever, -1 - no caching)**
* User prompt - a flag that says whether a server should send a push to a client **(true/false)**
* Api label - a label that says what api sent a message. Adds bussines value to provide message metrics. **(sign, chat, auth, push)**

## Policies

| Policy 	| Publishing <br>retrials 	| Client-side <br>caching duration 	| Server-side <br>caching duration  	|            User prompt            	| API label 	|
|:------:	|:-----------------------:	|:--------------------------------:	|:---------------------------------:	|:---------------------------------:	|:---------:	|
|    1   	|            3x           	|                -1*                	|                 6h                	| true<br>when<br>wc_sessionRequest 	|    sign   	|
|    2   	|            3x           	|                8h                	|                 0*                 	|   true<br>when<br>wc_chatMessage  	|    chat   	|

\* 0 - _forever_ ; -1 - _no caching_

## Publish payload

Publish payload constructs a data structure that is to send over the network. The server-side policy for a message is defined in the policy object.

```jsonc
{
	"id" : "1",
	"jsonrpc": "2.0",
	"method": string,
	"params" : {
		"topic" : string,
		"message" : string,  
		"ttl" : Int64,
        "policy": {
            "caching_duration" : Int64,
            "api" : string, 
            "promtp" : boolean,
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

The policy object defines the policy's parameters for both the client and server side.

```jsonc
{
    "retrials" : number,
    "client_caching_duration" : Int64,
    "server_caching_duration" : Int64,
    "api" : string,
    "promtp" : boolean,
}
```