# Relay Network

Relay network is defined by the federation of relay servers exchanging messages to route from a publishing client to a subscribing client on the same topic.

## Goals

Relay servers will route messages through other servers in the network that also have matching subscribers on a topic whilst maintaing the same expected behavior described by a publisher when requested to its own server

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
