
# Relay message policies

In this document we decribe message policies for each of WalletConnect APIs. The main focus is to provide message policies under unstable networking conditions and consider offline support for following APIs.

All policies are composed of different categories that define the behaviour when there is no web-socket nor Internet connection.

All the consideration is about the client (Kotlin, Swift, JS) part of given protocol. 

## Sign API

### Cache

* Published messages: Sign API do not cache published messages over the network. 

* Unpublished messages: Sign API do not cache unpublished messages over the network. When the message cannot be published, an error is returned to the client.

### Retry mechanism

* Sign API do not provide retry mechanism for unpublished messages. When the message cannot be published, an error is returned to the client. Client is responsible for sending the message again.

### Error handling

* If a message cannot be published, because of any reason, RelayClient must return an error to the Sign client.

### Network conditions

* SDK detects changing networking conditions. In terms of both no web-socket and no Internet connection an error is returned to the Sign client. 


## Chat API

### Cache

* Published messages: Chat API do not cache published messages over the network. (**TBD: Should we cache all messages locally? Or it is a part of our cloud architecture? Or should we consider both approaches?**)

* Unpublished messages: Chat API caches all unpublished messages in the persistent storage. When RelayClient detects that it is again possible to publish messages, those are pubished in the oryginal order. 

### Retry mechanism  

* Chat API provides a retry mechanism, where the RelayClient tries to send a message 3(**TBD: 3x or more?**) times until it is cached in the local persistent storage. When the RelayClient detects that it is again possible to publish messages, those are pubished in the oryginal order. 

### Error handling

* If a message cannot be published and a retry mechanism failed, the RelayClient must return an error to the Chat client.

### Network conditions

* SDK detects changing networking conditions. In terms of both no web-socket nor Internet connection and if a retry mechanism failed  an error is returned to the Chat client.


## Auth API

### Cache

* TBD

### Retry mechanism  

* TBD

### Error handling

* TBD


## Push API

### Cache

* TBD

### Retry mechanism

* TBD  

### Error handling

* TBD
