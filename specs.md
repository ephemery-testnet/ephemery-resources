# Ephemeral testnet - Specs

This document drafts specifications for an automatically reset ephemeral testnet. Learn more about the concept in the [original proposal](https://notes.ethereum.org/@mario-havel/stakers-testnet). 

In order to support the testnet, clients need to implement the following features: 

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

To connect to the current instance of the network, client must implement the genesis function. This function defines how the client stores information about the testnet and generates the current genesis. With each reset, network starts from a new genesis which needs to build based on given parameters, and correspond in EL and CL clients.

The main changes in the genesis iteration are chainId, timestamp and the withdrawal credentials of the first validator.
    
The network always starts from a genesis which is deterministically created based on the original one, genesis_0. Expiration of the genesis is given by its `genesis_timestamp` and `period` (a predefined constant, length of time a single ephemeral network runs). Therfore once the currenttimestamp reaches the terminal time of ephemeral network, it has to switch to a new genesis. This can be expressed as:

    let terminal_timestamp = genesis_timestamp + period
    
    if (current_timestamp >= terminal_timestamp):
       return new_genesis
    else:
       return none

Clients shall include a hardcoded genesis_0, similarly to other predefined networks. But this genesis is only used at the very beginning of the testnet existence, in its first iteration 0. Later on, with iteration 1 and further, client does not initialize this genesis but uses it to derive the current one. 

Thus, for any iteration i, after the first iteration (i > 0):

    genesis_timestamp_i = period * i + genesis_timestamp_0.

When the client starts with the option of ephemeral testnet, it checks whether a genesis for the network is present. If it doesn't exist or the current genesis timestamp is older than genesis_timestamp + period, it triggers the generation of a new genesis. This new genesis, derived from the genesis 0, will be written to the database and used to run the current network.

      let genesis = genesis_from_database()
      
      if (genesis = 0 || current_timestamp >= min_genesis_time + period):
         generate new_genesis 
         return genesis_from_database(new_genesis)
      else:
         return genesis_from_database()

The choice of period can be very important and have certain implications on netowrk stability and overall user experience. 

For example shorter periods offer:
   * Faster iterations for development and testing
   * Less bloat accumulation
   * Easier bug resolution
     
While longer iteration is useful for:
   * Longer testing windows
   * Running applications that require longer running histories
   * A more stable network environment
     

#### Execution client

Given a known period and current timestamp, client can always calculate the number of lifecycle iterations from genesis 0 and create a new genesis with latest parameters.

For example, let the current state of the network be: 
    
    ChainId: 39438088
    Genesis Zero Timestamp: 1691694000 (Mon Nov 14 19:00:00 UTC 2022)
    Current Timestamp: 1692036000 (Mon Aug 14 19:00:00 UTC 2023)
    Period: 604800 seconds (1 week)

* Number of iterations:
    *  `i` = `int((current_timestamp` - `genesis_0_timestamp) / period)`
    *  `i` = `int((1692036000 - 1668448800) / 604800)`
    *  `i` = `39`
* Timestamp of current genesis:
    * `genesis_timestamp` = `period` * `i` + `genesis_0_timestamp`
    * `genesis_timestamp` = `604800` * `39` + `1668448800`
    * `genesis_timestamo` = `1692036000`
* Current EL ChainId:
    * `chainId` = `genesis_0_chainId` + `i`
    * `chainId` = `39438088 + 39`
    * `chainId` = `39438127`
 
Staying in sync with rapidly changing genesis values like this will introduce new challenges to clients like:
   * Handling sync frequency
   * Managing data persistence
   * User experience
   * Infrastructure management
   
Mitigating this challenges will require solutions like:
   * Automatic updates that minimize the need for manual intervention.
   * Graceful transitions that minimize netowrk disruptions during resets.
   * Flexible configurations that allows users set how often they want to check for updates
   * Clear documentation that helps users understand the need for frequenct changes and how to effectively manage them.
   * A user notification system, like a simple countdown showing how much time before the upcoming network reset.
   

#### Consensus client

Genesis generation in CL client uses the same parameters as EL, while also requiring the generation of the updated genesis state in SSZ format, with minor changes which include:

* A deposit (smart) contract responsible for managing the process of validators making deposits to participate in the network.
  
   * During each iteration, the CL client generates a new set of validators based on trusted entities within the community.
     
   * The deposit contract is set up in the updated genesis state to reflect this new set of validators, ensuring that the network starts with the correct validator set.
     
* Maintaining static `ForkVersions` for tooling support, where the withdrawal credentials of the first validator in the set is overridden (this way there is still a unique `ForkDigest` for each iteration).
  
   *  The withdrawal credentials of the first validator is calculated using the formula: `0x0100000000000000000000000000000000000000000000000000000000000000 + i`.
     
   *  While the first withdrawal uses the initial withdrawal credentials `0x1000...`, subsequent iterations use a `i` value that is `1` greater than the previous value.  
     
   *  Usually, overriding withdrawal credential is dangerous because the first validator is responsible for initializing the network while providing the first few blocks. However, it's neccessary in this situation because:

      *  Withdrawal credentials plays a role in the identification and verification of validators' and overriding it here ensures that the first validator changes with each iteration as reflected in the associated credentials.
  
   *  Static `ForkVersions` here reduces unnecessary complexity and potential compatibility issues.
     
   *  While, a unique `ForkDigest` prevents replay attacks and ensures that each iteration is distinct.
        
*  Hashing the genesis validators root, calculated using the formula:

   *  `genesis.genesis_validators_root = hash_tree_root(genesis.validators)`

   *  The `hash_tree_root` function calculates the cryptographic hash of the `genesis.validator` set and returns the hash as a 256-bit value. This hash is then assigned to the `genesis_validators_root` field of the `genesis` struct.
   
* Setting the `genesis_timestamp` to the latest genesis timestamp and including a small `genesis_delay` for stability during the transition.
  

### Reset

The reset function defines an automatic process of throwing away the old data and starting with a new genesis. It depends on the previously defined function for genesis generation and client should implement it to automatically follow the latest network iteration.

For the reset function, we introduce `terminal_timestamp` value which marks when the network expires. It can be the same genesis timestamp of the next iteration or can be calculated simply as `terminal_timestamp = genesis_timestamp + period.`

When the network reaches a slot with a `current_timestamp >= terminal_timestamp:`

   * Client stops accepting/creating new blocks
      * This should be implemented without further functions to create a minimal version which is safe from forks
   * Current genesis, all blockchain and beacon data are discarded
   * Client triggers Genesis function (defined above):
      * Like on regular client startup, if genesis is not present
      * New genesis is written into db and initialized
   * After new genesis time is reached, network starts again from the new genesis

Clients should be able to do this without restarting, operating the network fully independently and with minimal downtime. This is necessary for infrastructure providers but redundant for users just joining the network for a short term testing. 


> *More thoughts on how Ephemery will possibly affect clients and infrastrucre providers can be found [here](https://twitter.com/remy_roy/status/1588613245321072643) and [here](https://ethereum-magicians.org/t/testnet-workgroup-paths-out-of-the-goerli-supply-mess/11453/4)*


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

