# Introduction

[Substrate] is a blockchain and smart contract framework written in the Rust programming language.
The InterPlanetary File System (or [IPFS]) is a distributed, peer-to-peer,
content-addressed storage framework.

Substrate has many features that differentiate it from a typical blockchain framework. One
such feature is [Off-Chain Workers], which are separate wasm execution contexts that run alongside
your blockchain node’s runtime, enabling you to make send and receive data from external sources,
such as HTTP APIs.

Thinking about an Off-Chain Worker in the context of IPFS led us to develop this functionality,
called `offchain::ipfs`. This gives you a reduced, but still powerful, subset of IPFS commands
you can call right from your pallet, on block creation, to achieve things like:

- Bytes -> Content ID (CID) data storage
- CID -> Bytes data storage
- Peer and content discovery via the distributed hash table (DHT)
- Libp2p connection and swarming with other IPFS peers
- Pinning and unpinning CIDs

This material and Rust IPFS itself are presented by the rs-ipfs team:
[@koivunej], [@ljedrz], [@whalelephant], and [@aphelionz]

TODO: Video embed in book

## A quick note about `node-template`

The primary value of this work is the embedded IPFS node itself and its Off-chain Worker APIs.

We reference the `node-template` pallet and executable throughout this manual. This included pallet
is meant to be a showcase of the embedded IPFS node, and is just one of many possible integrations
and uses cases that are possible. More IPFS functionalities could be exposed, and the ones exposed
could be improved and personalized further.

Finally, while we are moving towards documenting and releasing this work in a more official
capacity, `offchain::ipfs` should still be considered an alpha preview.

[Substrate]: https://substrate.io
[IPFS]: https://ipfs.io
[Off-Chain Workers]: https://www.substrate.io/kb/learn-substrate/off-chain-workers
[@koivunej]: https://github.com/koivunej
[@ljedrz]: https://github.com/ljedrz
[@whalelephant]: https://github.com/whalelephant
[@aphelionz]: https://github.com/aphelionz
