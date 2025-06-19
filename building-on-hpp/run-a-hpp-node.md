---
icon: circle-nodes
---

# Run a HPP Node

### Minimum hardware configuration

The following is the minimum hardware configuration required to set up a Nitro full node (not archival):

| Resource     | Recommended                                   |
| ------------ | --------------------------------------------- |
| RAM          | 16 GB                                         |
| CPU          | 4 core CPU (for AWS, a t3 xLarge instance)    |
| Storage Type | NVMe SSD drives are recommended               |
| Storage size | Depends on the chain and its traffic overtime |

Please note that:

* These minimum requirements for RAM and CPU are recommended for nodes that process a small amount of RPC requests. For nodes that require processing multiple simultaneous requests, both RAM and number of CPU cores will need to be scaled with the amount of traffic being served.
* Single core performance is important. If the node is falling behind and a single core is 100% busy, it is recommended to update to a faster processor
* The minimum storage requirements will change over time as the chain grows. Using more than the minimum requirements to run a robust full node is recommended.

### Prerequisites

Download and install [Docker](https://www.docker.com/), ensure it is running.

#### Step 1. Download chaininfo file

A [`chaininfo.json`](https://api.conduit.xyz/file/getArbitrumChainInfo?network=3adca3f7-bd91-4535-aca7-7cce30c8d822\&organization=a5d362cc-9b26-4bd0-b658-08581b2e1481) file is required to run the node.

#### Step 2. Run the node

Specify a local path to store data for running the node, determine the Arbitrum Nitro version, and then execute it. The contents of the `chaininfo.json` file downloaded above have been included in the docker run command.

The node should now be running and looking for peers to sync.

```bash
docker run --rm -it \
  --name=hpp-node \
  -v /local-directory/arbitrum:/home/user/.arbitrum \
  -p 0.0.0.0:8547:8547 \
  -p 0.0.0.0:8548:8548 \
  offchainlabs/nitro-node:v3.6.7-a7c9f1e \
  --parent-chain.connection.url=https://ethereum-sepolia-rpc.publicnode.com \
  --parent-chain.blob-client.beacon-url=https://ethereum-sepolia-beacon-api.publicnode.com \
  --chain.info-json='[{"chain-id":181228,"parent-chain-id":11155111,"chain-name":"conduit-orbit-deployer","chain-config":{"chainId":181228,"homesteadBlock":0,"daoForkBlock":null,"daoForkSupport":true,"eip150Block":0,"eip150Hash":"0x0000000000000000000000000000000000000000000000000000000000000000","eip155Block":0,"eip158Block":0,"byzantiumBlock":0,"constantinopleBlock":0,"petersburgBlock":0,"istanbulBlock":0,"muirGlacierBlock":0,"berlinBlock":0,"londonBlock":0,"clique":{"period":0,"epoch":0},"arbitrum":{"EnableArbOS":true,"AllowDebugPrecompiles":false,"DataAvailabilityCommittee":true,"InitialArbOSVersion":32,"InitialChainOwner":"0x3324DC1E72Ee0C0D0483503B5d36A592bfC862D9","GenesisBlockNum":0}},"rollup":{"bridge":"0x1DDe0F57E7889B6866505634E58E3057b01dfed0","inbox":"0xAAD45a7bF65b43E56767CdE3Ab84A5433c714Afc","sequencer-inbox":"0x7A6398deA2adc6fe4A3cfBA3352840bB03e440d3","rollup":"0x60b33120F5572608CC33c5C3a40c992987B59Edc","validator-utils":"0x9d502DD38E6E7FBdd3b7e964345d544ec37f1D72","validator-wallet-creator":"0x684A827456373a0C0379B1C82BA31Ee5E4F88F62","deployed-at":8539104}}]' \
  --chain.name=conduit-orbit-deployer \
  --node.feed.input.url=wss://relay-hpp-sepolia-turdrv0107.t.conduit.xyz \
  --execution.forwarding-target=https://rpc-hpp-sepolia-turdrv0107.t.conduit.xyz \
  --node.data-availability.enable \
  --node.data-availability.rest-aggregator.enable \
  --node.data-availability.rest-aggregator.urls=https://das-hpp-sepolia-turdrv0107.t.conduit.xyz \
  --http.api=net,web3,eth \
  --http.corsdomain="*" \
  --http.addr=0.0.0.0 \
  --http.vhosts="*"
```

HPP generates blocks only when there are transactions to process. So if you don't see new blocks coming in, it just means there's no transaction activity - your node is still syncing correctly.
