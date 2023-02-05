# Ephemeral Testnet


**Starting at iteration #60, Shanghai/Capella hard fork is scheduled for epoch 5 in every upcoming iteration. Update your clients to [versions from Zhejiang testnet](https://notes.ethereum.org/@launchpad/zhejiang#Which-versionbranch-do-I-use).**

The objective of this effort is to create the first automatically reset ephemeral Ethereum testnet which would serve as a cross-client testing network without issues stemming from a long-term running history.

## Proposal

An ephemeral testnet is a single network that rolls back to the genesis after a set period of time. This kind of network is focused on short term and heavy testing usecases. The purpose of this is also to avoid problems like insufficient testnet funds, inactive validators, state bloat, and similar issues faced by long-running testnets.

Learn more about the idea in the [original proposal](https://notes.ethereum.org/@mario-havel/stakers-testnet) and [specification](/specs.md). 


## Meta, network info

|                  | Current value       |
| ---------------- | ------------------- |
| Network ID       | 1337558             |
| Iteration number | 60                  |
| Rollback period  | 2 days              |
| Next rollback    | Feb 07 19:00:00 UTC |

### Landing page

https://ephemery.pk910.de/ (add RPC with single click)

### RPC providers

- https://rpc.bordel.wtf/test (geth)
- https://otter.bordel.wtf/erigon 
- https://eth.ephemeral.zeus.fyi (lighthouse-geth, full beacon)

### Faucets

- https://faucet.bordel.wtf/
- https://ephemery-faucet.pk910.de/

### Block explorers

- https://otter.bordel.wtf/
- https://explorer.ephemery.pk910.de/

### Beacon explorers

- https://beaconchain.ephemery.pk910.de/
- https://checkpointz.bordel.wtf/ (Checkpoint sync) 

### Validators

- [Launchpad](https://ephemery.launchpad.remyroy.com/en/)
- https://github.com/remyroy/staking-deposit-cli/releases/tag/v2.3.0.ephemery CLI

## Resources and contributing

The network is currently in its early stages, it runs as a small network with a manual reset mechanism. Each reset changes network parameters and requires a new genesis. Setup and reset mechanism is coordinated via [this repository](https://github.com/pk910/test-testnet-repo). Each release contains values for the new release. 

To run a node and participate in the network, use setup from [this repository](https://github.com/pk910/test-testnet-scripts). There are various deployment options, you can either use a [Docker setup](https://github.com/pk910/test-testnet-scripts#docker), cloud-init script or manually modify the `retention.sh` script to suit your system and run it as a cron job. There are also [instructions for manually setting up a node](https://github.com/pk910/test-testnet-scripts#manual-deployment). 

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

Ephemeral testnet needs a lot of research and development to be stable and widely usable. Here is a simple roadmap and current progress:

- [x] Proposal and initial discussion
- [x] Private network with manual reset mechanism
    - Using external script to reset the network
    - A small private chain was created to identify feasibility issues and to stabilize the devops setup.
- [x] Public network with manual reset mechanism
    - Stable network open to the public and wider debugging, simplifing node/validator setup
- [x] Creating general specifications for automatically reset ephemeral testnet 
    - Discussion about implementation details and specifications based on data gathered from the network with manual resets
    - [Specs draft](./specs.md)
    - [ ] Draft EIP
- [ ] Automatic onchain infrastracture
    - [x] Refuding validators
    - [ ] Deploying primitives for daps
- [ ] Preliminary client implementation work 
    - Single client pair, testing on private/public chain 
    - Improving and finishing specs 
- [ ] Cross client implementation 
    - Testing all client combos, ACD discussion


![](./bttg.png)
