
# Relay message policies

## Purpose
The purpose of this document is to define the message policies on both client and server sides.

## Definition

The message policy is a set of rules/actions that have to be taken to a message by involved parties without decrypting the message. Those rules are restricted by the list of parameters shown below.

## Parameters

The message policy is defined by the list of parameters, which describe the way of handling a given message. 

* Publishing retrials - defines how many tries have to be taken when publishing a message fails
* Caching - a flag that says whether a party should cache messages **(true/false)**
* Caching duration - defines a caching duration in **seconds**. (0 - forever)
* User prompt - a flag that says whether a server should send a push to a client **(true/false)**
* Api label - a label that says what api sent a message **(sign, chat, auth, push)**

## Policies

| Policy 	| Publishing <br>retrials 	| Client-side <br>caching 	| Client-side <br>caching duration 	| Server-side<br>caching 	| Server-side <br>caching duration  	|            User prompt            	| API label 	|
|:------:	|:-----------------------:	|:-----------------------:	|:--------------------------------:	|:----------------------:	|:---------------------------------:	|:---------------------------------:	|:---------:	|
|    1   	|            3x           	|          false          	|                 -                	|          true          	|                 6h                	| true<br>when<br>wc_sessionRequest 	|    sign   	|
|    2   	|            3x           	|           true          	|              30 days             	|          true          	|                 0*                 	|   true<br>when<br>wc_chatMessage  	|    chat   	|

\* _forever_

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
		"ttl" : Int64, 
        "policy": {
            "caching" : boolean,
            "caching_duration" : Int64,
            "promtp" : boolean,
		    "api" : string
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