# Relay Network

Relay network is defined by the federation of relay servers exchanging messages to route messages from a publishing client to a subscribing client on the same topic.

## Goals

Relay servers will route messages between publishers and subscribers of a topic in the same server and route them to other servers in the network that also have matching subscriptions

## Iridium Message

An Iridium message is defined as a serialized message of a successfully cached message from a publishing request from a connected client with one of the servers.

    iridium_message = prefix + version + tag + prompt + timestamp + length + message
    prefix          = utf8("irn")
    version         = uint8
    tag             = unit8
    prompt          = boolean
    timestamp       = uint64
    length          = uint64
    message         = utf8
