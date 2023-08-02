# Controller

A client can be either a controller or non-controller. This means that all sequences responded and/or proposed by this client will be controlled by it.

A controller is not bounded by the permissions set by the sequence, meaning it can send any JSON-RPC request, emit any notification type and is the only participant that upgrade the permissions or update the state of the sequence.

The controller client will always be the "wallet" which is exposing blockchain accounts to a "dapp" and therefore is also in charge of signing.

Disconnecting is however not exclusive to the controller client and can be triggered by either participants.
