# Building from Source

You can build the docker image from source, or build the binaries locally. Both of these
can take quite a long time, even on "developer" hardware - particularly the docker image.

## Running the node from source

``` bash
git clone https://github.com/uddugteam/substrate
cd substrate
git checkout offchain-ipfs-v0.3
cargo build --workspace
```

## Building the docker image from source

This is a multistage build based on Alpine linux. The resulting image will contain
the `substrate-ipfs` binary.

We suggest you supply your own tag name.

```bash
git clone https://github.com/uddugteam/substrate
cd substrate
git checkout offchain-ipfs-v0.3
docker build --file .maintain/Dockerfile --tag [your-tag-here] .
```

Refer to the **[Using the Docker image]** section of for information about running the image.

[Using the Docker image]: ./using-the-docker-image.md

Read on to learn about the modifications in the `offchain_ipfs` branch, and what they do.
