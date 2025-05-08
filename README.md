# Ephemeral Testnet

![](./bttg.png)


The objective of this effort is to create the first automatically reset ephemeral Ethereum testnet which would serve as a cross-client testing network without issues stemming from a long-term running history.

> [!WARNING]
> __Ephemery will activate Pectra hardfork on epoch 10 starting iteration 3943814, February 13 7PM UTC.__ Make sure to upgrade your clients and validators to latest Pectra release candidates! [Read more about the Pectra activation](#pectra).

## Proposal

An ephemeral testnet is a single network that rolls back to the genesis after a set period of time. This kind of network is focused on short term and heavy testing usecases. The purpose of this is also to avoid problems like insufficient testnet funds, inactive validators, state bloat, and similar issues faced by long-running testnets.

More resources: 
- [original proposal](https://notes.ethereum.org/@mario-havel/stakers-testnet)
- [specification](/specs.md)
- [EthPrague talk](https://www.youtube.com/watch?v=HUuR1k25-3g)
- [Blog article](https://medium.com/coinmonks/new-testing-environment-for-ethereum-stakers-e271d1f954eb)

## Meta, network info

|                  | Current value       |
| ---------------- | ------------------- |
| Network ID       | ![ChainId](https://img.shields.io/badge/dynamic/json?url=https%3A%2F%2Fephemery.dev%2FlatestInfo.php&query=%24.chainid&label=%20&color=gray) |
| Iteration number | ![Iteration](https://img.shields.io/badge/dynamic/json?url=https%3A%2F%2Fephemery.dev%2FlatestInfo.php&query=%24.iteration&label=%20&color=gray) |
| Rollback period  | 28 days              |
| Next rollback    | ![Dynamic JSON Badge](https://img.shields.io/badge/dynamic/json?url=https%3A%2F%2Fephemery.dev%2FlatestInfo.php&query=%24.resetTimeReadable&label=%20&color=gray) |

### Landing page

https://ephemery.dev/ (add RPC with single click)

### RPC providers

- https://rpc.bordel.wtf/test (geth)
- https://otter.bordel.wtf/erigon 
- https://eth.ephemeral.zeus.fyi (lighthouse-geth, full beacon)

### Faucets

- https://faucet.bordel.wtf/
- https://ephemery-faucet.pk910.de/
- https://api-faucet.bordel.wtf/

### Block explorers

- https://otter.bordel.wtf/
- https://explorer.ephemery.dev/

### Beacon explorers

- https://beaconchain.ephemery.dev/
- https://ephemery.info/
- https://beaconlight.ephemery.dev/

### Checkpoint sync endpoints

- https://checkpointz.bordel.wtf/
- https://ephemery.beaconstate.ethstaker.cc/
- https://checkpoint-sync.ephemery.ethpandaops.io/
- https://beaconstate-ephemery.chainsafe.dev/

### Validators

- [Launchpad](https://launchpad.ephemery.dev/)
- [ethstaker-deposit-cli](https://github.com/remyroy/ethstaker-deposit-cli/releases/tag/v0.1-ephemery)

## Run a node

At this moment, most official client releases don't include predefined Ephemery testnet. Check this overview of [supported clients](/client-implementations.md). To connect other clients to Ephemery network, it has to be done mannualy. 

Download latest Ephemery parameters from its [release](https://github.com/ephemery-testnet/ephemery-genesis/releases/) and point your client to it like with any custom network. 
```
wget https://github.com/ephemery-testnet/ephemery-genesis/releases/download/ephemery-111/testnet-all.tar.gz
mkdir ephemery && tar -xzf testnet-all.tar.gz -C ephemery
```
Example setup for geth and lighthouse. First initialize geth genesis and then run clients with configuration pointing to downloaded testnet files and variables:
```
geth --datadir geth-ephemery init ephemery/genesis.json 
source ephemery/nodevars_env.txt
geth --datadir geth-ephemery --authrpc.jwtsecret=/tmp/jwt --bootnodes $BOOTNODE_ENODE 
lighthouse bn -t ephemery --execution-endpoint http://localhost:8551 --execution-jwt=/tmp/jwt --boot-nodes=$BOOTNODE_ENR_LIST
```

This connects your client to the current Ephemery iteration. To connect to the latest iteration of the network after the client already ran a previous one, you need to use a different datadir or delete the existing one. 

To keep up with the reset and run your node constantly switching to latest network, e.g. for running public infrastracture, use [retention script](https://github.com/ephemery-testnet/ephemery-scripts/blob/master/retention.sh).

###  Deployment Tools
- [💊 EthPillar: one-liner setup tool and node management TUI](http://ethpillar.coincashew.com)
  - Choice of Nimbus/Nethermind or  Teku/Besu client pair

- Docker setups, services and more deployment options can be found in [Ephemery scripts repo](https://github.com/ephemery-testnet/ephemery-scripts).

### Pectra

Ephemery supports Pectra with iteration 3943814, February 13 7PM UTC. Starting with this iteration, Pectra is scheduled to activate in every upcoming iteration at epoch 10, around 8:14PM UTC. The first activation is before any other testnet, this might get bumpy but enables us to collect valuable testing data for client developers.

To run a node or a validator on Ephemery, use a Pectra compatible client. Not all clients will have a stable Pectra release, you might have to use a release candidate for testing or compile the latest version manually. 

As a staker, you will be able to test new features introduced in Pectra like validator aggregation thanks to [new MaxEB](https://eips.ethereum.org/EIPS/eip-7251). We increased the ETH payout in [faucets](https://faucet.bordel.wtf) to allow you to easily try this. 
As a user or developer, you can explore new possibilites of [account abstraction](https://eip7702.io/) and other [upgrades coming in Pectra](https://ethereum.org/en/roadmap/pectra).

## Resources and contributing

The network is currently in its early stages, it runs as a small network with a manual reset mechanism. Each reset changes network parameters and requires a new genesis. Setup and reset mechanism is coordinated via [this repository](https://github.com/ephemery-testnet/ephemery-genesis). Each release contains values for the new release. 

To run a node and participate in the network, use setup from [this repository](https://github.com/ephemery-testnet/ephemery-scripts). There are various deployment options, you can either use a [Docker setup](https://github.com/ephemery-testnet/ephemery-scripts#docker), cloud-init script or manually modify the `retention.sh` script to suit your system and run it as a cron job. There are also [instructions for manually setting up a node](https://github.com/ephemery-testnet/ephemery-scripts#manual-deployment). 

Checkout [current version of specs](./specs.md) for client implementation and feel free to provide feedback. 

### Contribute 

Contribute by participating in the network and identifying issues caused by the ephemeral setup. You can help a lot by testing features and infrastracture which might break during the reset. Here are few ideas for general testing:
  - Run a node, especially client which is not tested yet
  - Add validators, monitor attestations, proposed blocks, slashing issues
  - Use your wallet, find out how it behaves after the reset, how it handles different network ID from same RPC
  - Deploy dapps, try your favorite developer tooling 
  - Run infrastracture, explorers, etc
  - Reach out with your own ideas for testing

Feel free to open issue in this repository with your findings and ideas. And feel free to join the discussion in [Matrix room](https://matrix.to/#/#staker-testnet:matrix.org). 


## Roadmap

Ephemeral testnet needs a lot of research and development to be stable and widely usable. Here is a simple roadmap overview, for details on current tasks, check the [tracking issue](https://github.com/ephemery-testnet/ephemery-resources/issues). 

- [x] Proposal and initial discussion
- [x] Private network with manual reset mechanism
    - Using external script to reset the network
    - A small private chain was created to identify feasibility issues and to stabilize the devops setup.
- [x] Public network with manual reset mechanism
    - Stable network open to the public and wider debugging, simplifing node/validator setup
- [x] Creating general specifications for automatically reset ephemeral testnet 
    - Discussion about implementation details and specifications based on data gathered from the network with manual resets
    - [Specs draft](./specs.md)
    - [x] [EIP](https://eips.ethereum.org/EIPS/eip-6916) in review
    - [x] Validating specs in EIP
- [x] Automatic onchain infrastracture
    - [x] Refuding validators
    - [x] Deploying primitives for dapps
- [x] Preliminary client implementation work 
    - Single client pair, testing on private/public chain 
    - Improving and finishing specs 
- [ ] Cross client implementation 
    - Testing all client combos
    - ACD discussion between client teams

