# Ephemeral Testnet

The objective of this effort is to create the first ephemeral Ethereum testnet which would serve as a cross-client testing network without issues stemming from a long-term running history.

## Proposal

An ephemeral testnet is a single network that rolls back to the genesis after a set period of time. This kind of network is focused on short term and heavy testing usecases. The purpose of this is also to avoid problems like insufficient testnet funds, inactive validators, state bloat, and similar issues faced by long-running testnets.

Learn more about the idea in the [original proposal](https://notes.ethereum.org/@mario-havel/stakers-testnet). 

## Meta, network info

|                  | Current value       |
| ---------------- | ------------------- |
| Network ID       | 1337522             |
| Iteration number | 23                  |
| Rollback period  | 2 days              |
| Next rollback    | Nov 25 19:00:00 UTC |

### RPC providers

- https://rpc.bordel.wtf/test

### Faucets

- https://ephemery-faucet.pk910.de/

### Block explorers

### Beacon explorers

## Resources and contributing

The network is currently in its early stages, it runs as a small network with a manual reset mechanism. Each reset changes network parameters and requires a new genesis. Setup and reset mechanism is coordinated via [this repository](https://github.com/pk910/test-testnet-repo). Each release contains values for the new release. 

To run a node and participate in the network, use setup from [this repository](https://github.com/pk910/test-testnet-scripts). You have to modify the `retention.sh` script to suit your setup and run it as a cron job or in a loop. 

### Contribute 

Help us by participating in the network and identifying issues caused by the ephemeral setup. Try running the node, using your wallet and other infrastracture. 

Join discussion in [Matrix room](https://matrix.to/#/#staker-testnet:matrix.org). 

## Roadmap

Ephemeral testnet needs a lot of research and development to be stable and widely usable. Here is a simple roadmap and current progress:

- [x] Proposal and initial discussion
- [x] Private network with manual reset mechanism
    - Using external script to reset the network
    - A small private chain was created to identify feasibility issues and to stabilize the devops setup.
- [ ] Public network with manual reset mechanism
    - Stable network open to the public and wider debugging, simplifing node/validator setup
- [ ] Creating network specifications
    - Discussion about implementation details and specifications based on data gathered from the network with manual resets
- [ ] Preliminary client implementation work 
    - Single client pair, testing on private/public chain 
    - Improving and finishing specs 
- [ ] Cross client implementation 
    - Testing all client combos, ACD discussion


![](./bttg.png)
