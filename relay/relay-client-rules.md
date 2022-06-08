# Relay Client Rules

## Purpose

This document aims to define a set of rules that RelayClient should perform when publishing a message.

## Rules

Rules define the default behaviors that RelayClient must respect when publishing every message.

* Publishing retrials - given that a message publish fails. RelayClient must make three retries before throwing an error with a descriptive message to its consumer.
* Error handling - given that a message publish fails. After making three retries, RelayClient must throw an error with a descriptive message to its consumer.
* Offline support - given that RelayClient detects no Internet availability or web-socket connection. After making three retries, RelayClient must throw an error with a descriptive message to its consumer.

## Unpublished messages

The way of handling unpublished messages in the RelayClient should differ depending on the consumer.

* Given that the `api` field inside the `Policy` object equals `sign`: 
    * once RelayClient fails to publish a message for any reason, the unpublished message is not persistent in local storage. Sending a message again is on RelayClient's consumer.

<br>

* Given that `api` field inside the `Policy` object equals `chat`:
    * once RelayClient fails to publish a message for no Internet availability or web-socket connection, the unpublished message is persistent in local storage. Once RelayClient detects that connection is again available, messages are sent in the original order. For a better user experience, RelayClient should throw a descriptive error to its consumer.
    * once RelayClient fails to publish a message for any other reason than no available connection, the unpublished message is persistent in local storage, and RelayClient's consumer is responsible for sending a message again.


## FAQ

* What is a RelayClient? - an instance of Relay on the client-side used by any sdk instance.
* What are Publishing retrials? - a number of retrials that a RelayClient should perform when it fails to publish a message.
* What is an unpublished message? - an unpublished message is a message that failed to be sent over the network. 