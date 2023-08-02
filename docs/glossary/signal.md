# Signal

A signal is a payload that can be shared outside of the sequence (out-of-band) to communicate a proposal to another client. Each sequence has its own type of signal.

Pairing is a specialized sequence which has fixed permissions therefore the signal can be encoded as an URI using only the proposal topic, proposer's publicKey, relay protocol options and the controller flag for the proposer. This can be shared either through a qrcode or deep link between clients.

Session is a generalized sequence which has customized permissions therefore the signal is a proposal sent through a settled pairing already established between the two clients.