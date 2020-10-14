## Using the Docker image

Currently, the recommended way to use `offchain::ipfs` is via the Docker image provided by
[Equilibrium](https://equilibrium.co).

Simply pull the [eqlabs/substrate-ipfs] image from Docker Hub:

```bash
$ docker pull eqlabs/substrate-ipfs
```

The image comes with two binaries:

1. `node-template` - which serves as a preview pallet and reference implementation
2. `substrate-ipfs` - which is the real deal. Substrate infused with IPFS

## Running `node-template`

By default, the container will execute a command for starting the node-template binary with a
development chainspec. We expose the default websockets, rpc, p2p and prometheus ports from the
container to the host machine for interactions.

```bash
$ docker run \
    -p 9944:9944 \            # websockets
    -p 9933:9933 \            # rpc
    -p 30333:30333 \          # p2p
    -p 9615:9615 \            # prometheus
    -it \
    --rm \
    --name node-template \
    substrate-ipfs
```


## Running `substrate-ipfs`

```bash
$ docker run \
    -p 9944:9944 \            # websockets
    -p 9933:9933 \            # rpc
    -p 30333:30333 \          # p2p
    -p 9615:9615 \            # prometheus
    -it \
    --rm \
    --name sub-ipfs \
    substrate-ipfs \
    substrate                 # Override default command
```

## Container customizations

Several options are already available to tailor your container's runtime to suit your needs.

### Persistent Storage

To run with persistent storage volume between containers, first create a volume:

```bash
$ docker volume create substrate-ipfs-vol
```

Then add `-v substrate-ipfs-vol:/substrate-ipfs` to the docker run commands above.


### Running a custom command

To overwrite the default container commands, simply add the binary name followed by
flags, options, or subcommands to the end of the `docker run` command.

### Complete customization example

Putting it all together, a complete command with a persistent volume and a custom
executable command might look something like this:

For example, to display the help information from `substrate`

```bash
$ docker run \
    -p 9944:9944 \
    -p 9933:9933 \
    -p 30333:30333 \
    -p 9615:9615 \
    -it \
    --rm \
    --name sub-ipfs \
    -v substrate-ipfs-vol:/substrate-ipfs \       # mounted docker volume
    substrate-ipfs \
    substrate --help                              # override custom command
```

