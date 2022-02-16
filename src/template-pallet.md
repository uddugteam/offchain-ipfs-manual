# Example Pallet

`offchain::ipfs` comes with a showcase **[pallet]**, which is essentially a Rust module that
complies with the requirements to be included within a substrate **runtime**.

> **This pallet is meant only as an example.** We're including it to be helpful for future pallet authors
that want to use the embedded native IPFS node to suit their needs.

If you're familiar with Substrate and the Framework for Runtime Aggregation of Modularized Entities
(FRAME), you can simply view the [source code] for this pallet. Otherwise, read on as we go through
the code step by step.

Please note the order in which these concepts are explained here is not necessarily the order that
they appear in the code.

You can also learn more by following the [Building a Custom Pallet] tutorial.

[Building a Custom Pallet]: https://docs.substrate.io/tutorials/v3/proof-of-existence
[source code]: https://github.com/uddugteam/substrate/blob/offchain_ipfs-v0.3/bin/node-template/pallets/template/src/lib.rs
[pallet]: https://substrate.dev/docs/en/knowledgebase/runtime/pallets

## Prelude

We start by using items from the native runtime. Our pallet is `no_std` since we're targeting `Wasm`

```rust,ignore
#![cfg_attr(not(feature = "std"), no_std)]
// ...
use sp_core::offchain::{
  Duration, IpfsRequest, IpfsResponse, OpaqueMultiaddr, Timestamp
};
// ...
use sp_runtime::offchain::ipfs;
```

## Command Types

When your JSON-RPC calls are received by the pallet, the requests are
expressed as `___Command` enums and stored in the off-chain worker storage as
a queue, to be ingested by your native runtime and passed to IPFS.

Derive attributes are omitted.

```rust,ignore
// Commands involved in peer-to-peer connections
enum ConnectionCommand {
    ConnectTo(OpaqueMultiaddr),
    DisconnectFrom(OpaqueMultiaddr),
}

// Commands that add, remove, pin, unpin, and output data
enum DataCommand {
    AddBytes(Vec<u8>),
    CatBytes(Vec<u8>),
    InsertPin(Vec<u8>),
    RemoveBlock(Vec<u8>),
    RemovePin(Vec<u8>),
}

// Commands that query the distributed hash table (DHT)
// for peers and content
enum DhtCommand {
    FindPeer(Vec<u8>),
    GetProviders(Vec<u8>),
}
```

## The runtime configuration trait

The `system::Config` trait allows you to define which capabilities from the runtime you want to include,
and how you want to use them. You can also "tightly couple" your pallet to other pallets by adding their
`Config`s to your pallet's inherited trait list.

Here, however, we keep things simple by:

1. Loosely coupling this pallet by leaving out inherited traits
2. Including only the required `Event` type

```rust,ignore
/// The pallet's configuration trait.
#[pallet::config]
pub trait Config: CreateSignedTransaction<Call<Self>> + frame_system::Config {
    /// The overarching event type.
    type Event: From<Event<Self>> + Into<<Self as system::Trait>::Event>;
}
```

Later in the code, we implement some helper functions on the `Config` struct. The function
bodies are omitted for brevity's sake.

```rust,ignore
impl<T: Config> Pallet<T> {
    // "Sends" a request to the local IPFS node by adding it to the offchain storage
    fn ipfs_request(req: IpfsRequest, deadline: impl Into<Option<Timestamp>>)
      -> Result<IpfsResponse, Error<T>>

    // Reads from the `ConnectionQueue` and connects / disconnects
    // from desired / undesired peers, respectively
    fn connection_housekeeping() -> Result<(), Error<T>>

    // Reads `FindPeer` and `GetProviders` commands from the `DhtQueue`,
    // and requests their execution from the native runtime
    fn handle_dht_requests() -> Result<(), Error<T>>

    // Reads `AddBytes`, `CatBytes`, `DataCommand`, `RemoveBlock`, `InsertPin`,
    // and `RemovePin` commands from the `DataQueue` and requests their
    // execution from the native runtime.
    fn handle_data_requests() -> Result<(), Error<T>>

    // Logs metadata (the number of connected peers) to the console at the DEBUG log level
    fn print_metadata() -> Result<(), Error<T>>
```

## The `decl_` macros

Pallets included in Substrate runtimes must adhere to the conventions of [FRAME].

[FRAME]: https://substrate.dev/docs/en/knowledgebase/runtime/frame

### storage

Here, we define the data that will actually be stored on-chain when calling **extrinsics**.

Since the offchain-worker can't perform I/O outside of the wasm context, we store our requests as
queues, to be processed on a periodic basis, consumed, and ultimately performed by the native
runtime.

This is where we use the `ConnectionQueue`, `DataQueue`, and `DhtQueue` command types from above.

```rust,ignore
    // This pallet's storage items.
    #[pallet::storage]
    #[pallet::getter(fn connection_queue)]
    // A list of addresses to connect to and disconnect from.
    pub type ConnectionQueue<T: Config> = StorageValue<_, Vec<ConnectionCommand>, ValueQuery>;

    #[pallet::storage]
    #[pallet::getter(fn data_queue)]
    // A queue of data to publish or obtain on IPFS.
    pub type DataQueue<T: Config> = StorageValue<_, Vec<DataCommand>, ValueQuery>;

    #[pallet::storage]
    #[pallet::getter(fn dht_queue)]
    // A list of requests to the DHT.
    pub type DhtQueue<T: Config> = StorageValue<_, Vec<DhtCommand>, ValueQuery>;
```

### event

This is where we define what those events are and what they contain.

Once a command is sent to the off-chain worker, one of the following chain events is emitted.

```rust,ignore
    // The pallet's events
    #[pallet::event]
    #[pallet::generate_deposit(pub(super) fn deposit_event)]
    pub enum Event<T: Config> {
        /// Event documentation should end with an array that provides descriptive names for event
        /// parameters. [something, who]
        SomethingStored(u32, T::AccountId),
        ConnectionRequested(T::AccountId),
        DisconnectRequested(T::AccountId),
        QueuedDataToAdd(T::AccountId),
        QueuedDataToCat(T::AccountId),
        QueuedDataToPin(T::AccountId),
        QueuedDataToRemove(T::AccountId),
        QueuedDataToUnpin(T::AccountId),
        FindPeerIssued(T::AccountId),
        FindProvidersIssued(T::AccountId),
    }
```

### hooks

We should add some hooks that should be called on every new block.

```rust,ignore
#[pallet::hooks]
impl<T: Config> Hooks<BlockNumberFor<T>> for Pallet<T> {
    // Called at the beginning of every block before any extrinsics. Clears
    // `ConnectionQueue` and `DhtQueue` values every block, and clears
    // `DataQueue` every other block, since they should have been processed
    // Returns a weight of 0
    fn on_initialize(block_number: T::BlockNumber) -> Weight
    
    // Called at the beginning of every block to create extrinsics.
    // - `connection_housekeeping` and `handle_dht_requests` called every block
    // - `handle_data_requests` is called on every other block
    // - `print_metadata` is called every 5 blocks
    // blocks to alleviate some bandwidth and storage congestion
    fn offchain_worker(block_number: T::BlockNumber)
}
```

### call

This section, perhaps the most critical section of any given pallet, is where you can define
functions that are exposed via JSON-RPC to client libraries and, by proxy, your users.

In practice, the bulk of what these functions do is to modify the `DataQueue`, `DhtQueue`, and
`ConnectionQueue` storage objects by pushing signed command requests to their respective queues.

Some default types in the functions are omitted, but we've kept the `#[weight]` attributes around.

> The Substrate docs define one unit of weight as "one picosecond of execution time on
fixed reference hardware." These are essentially _time limits_ for block creation, and can be
(indirectly) mapped to transaction fees analogous to something like "gas fees." Read more about
[weights] if you're curious.

[weights]: https://substrate.dev/docs/en/knowledgebase/learn-substrate/weight

```rust,ignore
// The pallet's dispatchable functions.
// Dispatchable functions allows users to interact with the pallet and invoke state changes.
#[pallet::call]
impl<T: Config> Pallet<T> {
    /// Mark a `Multiaddr` as a desired connection target. The connection will be established
    /// during the next run of the off-chain `connection_housekeeping` process.
    #[pallet::weight(100_000)]
    pub fn ipfs_connect(origin: OriginFor<T>, addr: Vec<u8>) -> DispatchResult
    
    /// Queues a `Multiaddr` to be disconnected. The connection will be severed during the next
    /// run of the off-chain `connection_housekeeping` process.
    #[pallet::weight(500_000)]
    pub fn ipfs_disconnect(origin: OriginFor<T>, addr: Vec<u8>) -> DispatchResult
    
    /// Add arbitrary bytes to the IPFS repository. The registered `Cid` is printed out in the
    /// logs.
    #[pallet::weight(200_000)]
    pub fn ipfs_add_bytes(origin: OriginFor<T>, data: Vec<u8>) -> DispatchResult
    
    /// Find IPFS data pointed to by the given `Cid`; if it is valid UTF-8, it is printed in the
    /// logs verbatim; otherwise, the decimal representation of the bytes is displayed instead.
    #[pallet::weight(100_000)]
    pub fn ipfs_cat_bytes(origin: OriginFor<T>, cid: Vec<u8>) -> DispatchResult
    
    /// Add arbitrary bytes to the IPFS repository. The registered `Cid` is printed out in the
    /// logs.
    #[pallet::weight(300_000)]
    pub fn ipfs_remove_block(origin: OriginFor<T>, cid: Vec<u8>) -> DispatchResult
    
    /// Pins a given `Cid` non-recursively.
    #[pallet::weight(100_000)]
    pub fn ipfs_insert_pin(origin: OriginFor<T>, cid: Vec<u8>) -> DispatchResult
    
    /// Unpins a given `Cid` non-recursively.
    #[pallet::weight(100_000)]
    pub fn ipfs_remove_pin(origin: OriginFor<T>, cid: Vec<u8>) -> DispatchResult
    
    /// Find addresses associated with the given `PeerId`.
    #[pallet::weight(100_000)]
    pub fn ipfs_dht_find_peer(origin: OriginFor<T>, peer_id: Vec<u8>) -> DispatchResult
    
    /// Find the list of `PeerId`s known to be hosting the given `Cid`.
    #[pallet::weight(100_000)]
    pub fn ipfs_dht_find_providers(origin: OriginFor<T>, cid: Vec<u8>) -> DispatchResult
}
```

### errors

This is where we can define the myriad ways things can go wrong, as an enum.

```rust,ignore
    #[pallet::error]
    pub enum Error<T> {
        /// Error names should be descriptive.
        NoneValue,
        /// Errors should have helpful documentation associated with them.
        StorageOverflow,
        CantCreateRequest,
        RequestTimeout,
        RequestFailed,
    }
```

Read on to see examples of how you can make calls to the this example pallet from your application.
