# Using the Docker image

The recommended way to use `offchain::ipfs` is via the [andskur/substrate-offchain-ipfs] image.

[andskur/substrate-offchain-ipfs]: https://hub.docker.com/r/andskur/substrate-offchain-ipfs

## Installing the image

```bash
# Pull the image from Docker Hub
$ docker pull andskur/substrate-offchain-ipfs
```

The image comes with binary. The `substrate` binary does not have our custom
pallets to interact with the IPFS node,
instead you can connect to it through its multiaddr.

The image exposes ports `9944` for WebSockets, `9933` for RPC, `30333` for p2p, and `9615` for
Prometheus.

## Running the image

The default command for the image is:

`node-template --ws-external --rpc-external --base-path=/offchain-ipfs --dev`

Run the default with **dev** chain like so:

```bash
docker run \
  -p 9944:9944 \
  -p 9933:9933 \
  -p 30333:30333 \
  -p 9615:9615 \
  -it \
  --rm \
  --name sub-ipfs \
  andskur/substrate-offchain-ipfs \
  substrate --dev
```

This will work with any arguments you'd normally pass to `substrate`

### Persistent Storage

To run with persistent storage volume between containers, first create a volume:

```bash
docker volume create offchain-ipfs-vol
```

Then add `-v offchain-ipfs-vol:/substrate-offchain-ipfs` to the docker run commands above.
