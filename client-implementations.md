
### Client Implementations
The aim of this doc is to track implementation work done so far to provide native support for Ephemery on both the Execution and Consensus clients.

### EL Client Implementation Status

| **Client**   | **Status** | **Contributor(s)** | **Notes**                  |
|--------------|---------------------------|-----------------------|----------------------------|
| **[Besu](https://besu.hyperledger.org/en/stable/)**     | PR under review               | [@gconnect](https://github.com/gconnect)                    | - ✅  Native support without extra arguments completed. <br> - ✅  Genesis function implemented <br> - [ ] Genesis Reset Implementation <br> - [ ] Update Besu documentation <br> - [Detail note](https://hackmd.io/@gconnect/BJVMDpX6R)      | 
| **[Geth](https://geth.ethereum.org/)**     |Completed                 | [@atkinsonholly](https://github.com/atkinsonholly)                 |  - [Detail update](https://hackmd.io/@HOL/SJwLmrUmR)       |
| **[Reth](https://reth.rs/)**     | Completed                | [@T-ess](https://github.com/T-ess)                   | - [Detail update](https://hackmd.io/@teri-b/S1D6Np_Q6) |
| **[Nethermind](https://www.nethermind.io/)**     | Not Started                | -                     | -                   |
| **[Erigon](https://github.com/ledgerwatch/erigon)**     | Not Started                | -                     | -  | 
| **[EthereumJS](https://github.com/ethereumjs/ethereumjs-monorepo)**     | Not Started                | -                     | -  |


### CL Client Implementation Status

| **Client**   | **Status** | **Contributor(s)** | **Notes**                  | 
|--------------|---------------------------|-----------------------|----------------------------|
| **[Teku](https://consensys.io/teku)**     | Genesis Reset in-progress                | [@gconnect](https://github.com/gconnect)                  | - ✅  Native support without extra arguments merged. <br> - ✅ Genesis function implemented <br> - [ ] Genesis Reset Implementation in progress <br> - [ ] Update documentation <br> - [Detail note](https://hackmd.io/@gconnect/BJVMDpX6R)        | 
| **[Lodestar](https://lodestar.chainsafe.io/)** | Completed                | [@atkinsonholly](https://github.com/atkinsonholly)                 |  - [Github Discussion](https://github.com/ChainSafe/lodestar/issues/6064) <br>  - [Development update]( https://hackmd.io/@HOL/Hyp4bXfV6)    |
| **[Lighthouse](https://lighthouse.sigmaprime.io/)**| Completed                | [@T-ess](https://github.com/T-ess)                 | - [Detail update](https://hackmd.io/@teri-b/S1D6Np_Q6)     |
| **[Nimbus](https://nimbus.team/)**     | Not Started                | -                     | -  |
| **[Prysm](https://docs.prylabs.network/docs/getting-started/)**     | Not Started                | -                     | -  | 
| **[Grandine](https://docs.grandine.io/)**     | Not Started                | -                     | -  | 
