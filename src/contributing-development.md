# Contributing / Development

There are several ways that people can help `offchain::ipfs`.

## Create a dApp or a custom Substrate pallet, and tell us about it

Now that the proof-of-concept is complete and this manual is published, our goal is to be
laser-focused on user needs and to extend the capabilities of `offchain::ipfs` based on them.
So, **if you end up trying this out, we would love to know how, and what your use case is.**

- Tell us about what you're doing by opening an information issue at [rs-ipfs/substrate]
- Share feedback on this manual by opening an issue at [rs-ipfs/offchain-ipfs-manual]
- provide feedback on `offchain::ipfs` itself by opening an issue at [rs-ipfs/substrate]

## Implement more IPFS functionality

Some of the functionality that exists in [Rust IPFS] is not exposed via `offchain::ipfs`, and some
of the IPFS functionalities are not implemented in Rust IPFS at all. If there's something that another
IPFS implementation does that you'd like offchain::ipfs to do, let us know or take a crack at
implementing it yourself.

- Submit ideas for `offchain::ipfs` at [rs-ipfs/substrate].

[Rust IPFS]: https://github.com/uddugteam/rust-ipfs
[rs-ipfs/substrate]: https://github.com/uddugteam/substrate
[rs-ipfs/offchain-ipfs-manual]: https://github.com/uddugteam/offchain-ipfs-manual