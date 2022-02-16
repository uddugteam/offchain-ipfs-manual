# Introduction

> This doc is updated version of
  [offchain-ipfs](https://github.com/uddugteam/substrate/tree/offchain-ipfs-v0.3)
  for Substrate v3+.

`offchain::ipfs` is [Substrate], infused with [IPFS].

> **Substrate** is a blockchain framework built in Rust, with off-chain worker capabilities.
<br />**IPFS** is a distributed file storage network, connecting peers and their content.

By including [the Rust implementation IPFS](https://github.com/uddugteam/rust-ifps) in the native
Substrate runtime, and by allowing pass-through wasm calls via Substrate's
[Off-chain Workers], we enable a powerful and familiar subset of the IPFS APIs, including:

- `ipfs add` - Write data to IPFS
- `ipfs cat` - Read data from IPFS
- `ipfs dht findpeer` - Discover peers
- `ipfs dht findprovs` - Discover content
- `ipfs swarm connect` / `disconnect` - Swarm with other IPFS peers
- `ipfs pin add` / `rm` - Pin and unpin content

`offchain::ipfs` allows you to account for your data transactions and DHT status in the blockchain.
These on-chain insights can serve as a foundation for incentivized data storage and replication.
This means _no separate executable_: both blockchain and distributed storage are together in one.

The `offchain::ipfs` Manual is the documentation of our efforts, as well as useful explanations
and code examples to get you started using this technology. Due to `offchain::ipfs` being a
well-maintained fork of [paritytech/substrate], this manual also stands in as typical
documentation, such as docs.rs and README.md files.

This manual is presented by: [@koivunej], [@ljedrz], [@whalelephant], and [@aphelionz]

Manual have updated to Sybstrate v3 by: [@andskur] and [Uddùg team]

## Disclaimers

You should still consider this an **alpha preview**.

The primary value of this work is the embedded IPFS node itself. The pallet included in the
`node-template` binary is only meant as a showcase, and is just one of many possible realizations
of `offchain::ipfs`.

[paritytech/substrate]: https://github.com/paritytech/substrate
[Substrate]: https://substrate.io
[Polkadot]: https://polkadot.network
[Kusama]: https://kusama.network
[IPFS]: https://ipfs.io
[Off-Chain Workers]: https://docs.substrate.io/v3/concepts/off-chain-features/
[Uddùg team]: https://github.com/uddugteam
[@andskur]: https://github.com/andskur
[@koivunej]: https://github.com/koivunej
[@ljedrz]: https://github.com/ljedrz
[@whalelephant]: https://github.com/whalelephant
[@aphelionz]: https://github.com/aphelionz
