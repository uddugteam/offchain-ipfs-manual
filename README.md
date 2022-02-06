# The `offchain::ipfs` Manual

## Table of Contents

- [Usage](#usage)
- [Install](#install)
- [Contributing](#contributing)
- [License](#license)

This fork is based of original [offchain-ipfs-manual](https://github.com/rs-ipfs/offchain-ipfs-manual)
with updates to the latest Substrate version.

## Usage

Visit [https://uddugteam.github.io/offchain-ipfs-manual] to read the book.

[https://uddugteam.github.io/offchain-ipfs-manual]: https://uddugteam.github.io/offchain-ipfs-manual

## Install

You can install and run locally if you want to contribute.

First, install [`mdbook`](https://rust-lang.github.io/mdBook/cli/index.html). Then:

```bash
git clone https://github.com/uddugteam/offchain-ipfs-manual
cd offchain-ipfs-manual
mdbook serve
```

This will open up a version of the book on localhost, port 3000

## Contributing

PRs accepted.

To run linting locally, run:

```bash
npx markdownlint-cli src README.md
```

## License

TBD © 2022 Rust IPFS Maintainers & Uddùg Team
