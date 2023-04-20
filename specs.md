# Ephemeral testnet - Specs

This document drafts specifications for an automatically reset ephemeral testnet. Learn more about the concept in the [original proposal](https://notes.ethereum.org/@mario-havel/stakers-testnet). 

In order to support the testnet, clients need to implement the following features. 

## Features required by the testnet

There are two levels of how a client implementation can support this testnet. 

* Basic support requires the client to determine the current network specs and enables only connecting to the network. 
    * This means support of the Genesis function
    * Enough to participate in the network for short term testing
* Full support is client which can also follow the reset process and always sync the latest chain even during the reset. 
    * This would require also support of the Reset feature
    * Needed for running infrastructure, genesis validators and bootnodes

This approach is based on data gathered from public automatically resetted network [Ephemery](https://ephemery.dev/) and aims to be fully compatible with it. 

### Genesis 

With each reset, the network starts again from a new genesis. The new genesis is deterministically created based on the previous one. The main changes in the genesis are chainId, timestamp and the withdrawal credentials of the first validator. 

At the very beginning of testnet existence, in the first iteration, `period 0`, the network starts with `genesis 0`. This very fist genesis is hardcoded in the client like any other predefined network. However, during each client start, client does not initiatilize this genesis but rather builds a new one based on it. This new genesis, derived from the `genesis_0`, will be written to the database and used to run the current network. 

Given a known `period` (the length of time network instance will run), the client can calculate the number of lifecycle iterations from `genesis_0` to the current one and create a new genesis with latest parameters. Always, when the client starts with an option of ephemeral testnet, it checks the current genesis timestamp and identifies whether it's older than `timestamp + period`, at which point it triggers generation of a new genesis.

New genesis needs to be created and correspond in both EL and CL client. Manual implementation of this mechanism can be found in [this script](https://hackmd.io/@jimmygchen/rJw2Zjl2j). 

#### Execution client

* Number of iterations:
    *  `i` = `int((latest timestamp` - `genesis_0.timestamp) / period)`
* Timestamp of current genesis:
    * `genesis_timestamp` = `period` * `i` + `genesis_0.timestamp`
* Current EL ChainId:
    * `chainId` = `genesis_0.chainId` + `i`

#### Consensus client

Genesis generation in CL client uses the same parameters as EL but requires also updated genesis state in ssz. 

`MIN_GENESIS_TIME` is set to latest genesis timestamp, defines when the current period starts. It is recommended to add also a small `GENESIS_DELAY`, for example 5 minutes, to avoid issues while infrastracture is restarting on new genesis. 

In order to keep the `ForkVersions` of the network static for better tooling support, the withdrawal credentials of the first validator in the validator set need to be overridden by a calculated value (this way there is still a unique `ForkDigest` for each iteration).
* `genesis.validators[0].withdrawal_credentials` = `0x0100000000000000000000000000000000000000000000000000000000000000` + `i`
* `genesis.genesis_validators_root` =  `hash_tree_root(genesis.validators)`

It's up for discussion how to trigger these functions during the client start and how to store testnet values constants.
Further functions for network reset depend on these feature for generating new genesis.

### Reset

The reset defines process of rolling back to genesis. Client implementing this function will automatically follow the chain with each reset. In contrast, if client supports only the genesis generation function, user has to manually shut it down and delete the database.

When network reaches timestamp higher than current `genesis_timestamp` + `period`:

- Network stops accepting/creating new blocks
    - This can be implemented by itself to create a minimal version which is safe from forks 
- Current genesis, all blockchain data, state and ancient database is discarded 
- Client generates a new genesis based on the previous one using the Genesis function
- New genesis is initialized, written into new db
- After new genesis time is reached, network starts again from the new genesis

Clients should be able to do this without restarting, operating the network fully independently and with minimal downtime. This is necessary for infrastructure providers but redundant for users just joining the network for a short term testing. 

## Variables

- Period 
    - It should be predefined in the testnet configuration
    - Depends on number of validators in genesis. It needs to be high enough so network cannot be overtaken considering the time to active a validator
```
Genesis Validators => Epochs until < 66% majority
10k  => 1289 Epochs (5,7 days)
50k  => 6441 Epochs (28,6 days)
75k  => 9660 Epochs (42,9 days)
100k => 12877 Epochs (57,2 days)
150k => 19323 Epochs (85,9 days)
200k => 25764 Epochs (114,5 days)
```
- ChainId 
    - Iteration starting from predefined number.
    - shouldn't collide with any other existing EVM chain to prevent transaction replays.
```
Proposal: 2015102100
```

