# The architecture of `offchain::ipfs`

As we explained in the introduction, `offchain::ipfs` is currently a fork of
[`paritytech/substrate`] maintained by [Uddùg Team](https://uddug.com).

There are three branches of note:

- [`master`] - which will always follow `paritytech/substrate` in lock-step, with no modifications
- [`offchain_ipfs`] - which contains the modifications, and periodically rebases from `master`

There may be other branches at any given time for pragmatic purposes, but the three above should
always exist and be suitable for their respective roles.

In the rest of this chapter, we'll show you how to build the code in these branches, and then we'll
take a closer look at what modifications were made to substrate to achieve `offchain::ipfs`.

[`master`]: https://github.com/uddugteam/substrate
[`offchain_ipfs`]: https://github.com/uddugteam/substrate/tree/offchain-ipfs-v0.3
[`paritytech/substrate`]: https://github.com/paritytech/substrate
