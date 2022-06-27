# Relay Network

Relay network is defined by the federation of relay servers exchanging messages to route from a publishing client to a subscribing client on the same topic.

## Goals

Relay servers will route messages through other servers in the network that also have matching subscribers on a topic whilst maintaing the same expected behavior described by a publisher when requested to its own server

## Iridium Message

An Iridium message is defined as a serialized message of a successfully cached message from a publishing request from a connected client with one of the servers.


- **prefix** - a string prefix ("irn") for exchanged iridium messages.
- **version** - an integer number of the serialized schema for messages.
- **tag** - an integer label that identifies a message without knowing its contents.
- **prompt** - a flag that identifies whether the server should prompt a user action.
- **ttl** - an integer in seconds defining the duration a message should be cached.
- **timestamp** - an integer epoch when a message was observed by the publishing relay server.
- **length** - an integer defining the size of the message length.
- **message** - a plaintext message to be relayed to any subscribers on the topic.


Serialized byte schema (v1.0) would be as follows: 

    iridium_message = prefix + version + tag + prompt + timestamp + length + message
    prefix          = utf8  = "irn"
    version         = uint8 = 1
    tag             = uint8
    prompt          = boolean
    ttl             = uint64
    timestamp       = uint64
    length          = uint64
    message         = utf8
   
   
