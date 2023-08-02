# Settlement

Settlement refers to the internal event which both clients will execute from a successful response of a sequence proposal.

In the case of a responder, the settlement happens before the response is published to the proposer.

In the case of a proposer, the settlement happens after receiving the response published by the responder.

Settlement will generate a shared key using the key pairs of the participants and it will determine the topic from hashing the shared key which is only known to both participants