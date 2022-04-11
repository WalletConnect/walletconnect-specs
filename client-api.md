# Reference Client API

```typescript
abstract class Client {
  // ---------- Methods ----------------------------------------------- //

  // initializes the client with persisted storage and a network connection
  public abstract init(params: {
    metadata?: AppMetadata;
  }): Promise<void>;

	// for responder to pair a pairing created by a proposer
  public abstract pair(params: {
		uri: string;
	}): Promise<Sequence>;

  // for proposer to propose a session to a responder
  public abstract connect(params: {
    chains: string[];
    methods: string[];
    events: string[];
    pairingTopic?: string;
  }): Promise<Sequence>;

  // for responder to approve a session proposal
  public abstract approveSession(params: {
    proposerPublicKey: string;
    accounts: string[];
    methods: string[];
    events: string[];
  }): Promise<Sequence>;

  // for responder to reject a session proposal
  public abstract rejectSession(params: {
    proposerPublicKey: string;
    reason: Reason;
  }): Promise<void>;

  // for controller to update session accounts
  public abstract updateAccounts(params: {
    topic: string;
    accounts: string[]
  }): Promise<void>;

  // for controller to update session methods
  public abstract updateMethods(params: {
    topic: string;
    methods: string[]
  }): Promise<void>;

	// for controller to update session events
  public abstract updateEvents(params: {
    topic: string;
    events: string[]
  }): Promise<void>;

	// for controller to update session expiry
  public abstract updateExpiry(params: {
    topic: string;
    expiry: number
  }): Promise<void>;

  // for proposer to request JSON-RPC
  public abstract request(params: {
    topic: string;
    request: RequestArguments;
    chainId?: string;
  }): Promise<any>;

  // for responder to respond JSON-RPC
  public abstract respond(params: {
    topic: string;
    response: JsonRpcResponse;
  }): Promise<void>;

  // for either to ping and verify peer is online
  public abstract ping(params: {
    topic: string;
  }): Promise<void>;

  // for controller to send events
  public abstract emit(params: {
    topic: string;
    event: SessionEvent;
		chainId?: string;
  }): Promise<void>;

  // for either to disconnect a session
  public abstract disconnect(params: {
    topic: string;
    reason: Reason;
  }): Promise<void>;

  // ---------- Events ----------------------------------------------- //

  // subscribe to pairing proposal
  public abstract on("pairing_created", (pairingCreated: PairingCreated) => {}): void;

  // subscribe to session proposal
  public abstract on("session_proposal", (sessionProposal: SessionProposal) => {}): void;

  // subscribe to session request
  public abstract on("session_request", (sessionRequest: SessionRequest) => {}): void;

  // subscribe to session notification
  public abstract on("session_event", (sessionEvent: SessionEvent) => {}): void;
}
```
