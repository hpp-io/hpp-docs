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

#### Run the node

Specify a local path to store data for running the node, determine the Arbitrum Nitro version, and then execute it. The contents of the `chaininfo.json` file downloaded above have been included in the docker run command.

The node should now be running and looking for peers to sync.

#### **HPP Mainnet**

{% code lineNumbers="true" %}
```bash
docker run --rm -it \
  --name=hpp-node-mainnet \
  -v /local-directory/arbitrum:/home/user/.arbitrum \
  -p 0.0.0.0:8547:8547 \
  -p 0.0.0.0:8548:8548 \
  offchainlabs/nitro-node:v3.6.7-a7c9f1e \
  --parent-chain.connection.url=https://ethereum-rpc.publicnode.com \
  --parent-chain.blob-client.beacon-url=https://ethereum-beacon-api.publicnode.com \
  --chain.info-json='[{"chain-id":190415,"parent-chain-id":1,"chain-name":"conduit-orbit-deployer","chain-config":{"chainId":190415,"homesteadBlock":0,"daoForkBlock":null,"daoForkSupport":true,"eip150Block":0,"eip150Hash":"0x0000000000000000000000000000000000000000000000000000000000000000","eip155Block":0,"eip158Block":0,"byzantiumBlock":0,"constantinopleBlock":0,"petersburgBlock":0,"istanbulBlock":0,"muirGlacierBlock":0,"berlinBlock":0,"londonBlock":0,"clique":{"period":0,"epoch":0},"arbitrum":{"EnableArbOS":true,"AllowDebugPrecompiles":false,"DataAvailabilityCommittee":true,"InitialArbOSVersion":32,"InitialChainOwner":"0xF91B7476e52374dD75fb3d598C5f2D5dc019fc90","GenesisBlockNum":0}},"rollup":{"bridge":"0x9948eDFBb9e0b104bAd60393dBe79d0BC7937014","inbox":"0xE0400a87d5Ee8a2Fc1dF2aAf4B6d8f89d0B9bE55","sequencer-inbox":"0x9B26957a661bc862FA0d7eb21813Aa008d0Cc6E6","rollup":"0xf0d2960a37B33567FF7507C2d59da021277663A1","validator-utils":"0x84eA2523b271029FFAeB58fc6E6F1435a280db44","validator-wallet-creator":"0x0A5eC2286bB15893d5b8f320aAbc823B2186BA09","deployed-at":22943219}}]' \
  --chain.name=conduit-orbit-deployer \
  --node.feed.input.url=wss://relay-hpp-mainnet-xeajiyxsci.t.conduit.xyz \
  --execution.forwarding-target=https://mainnet.hpp.io \
  --node.data-availability.enable \
  --node.data-availability.rest-aggregator.enable \
  --node.data-availability.rest-aggregator.urls=https://das-hpp-mainnet-xeajiyxsci.t.conduit.xyz \
  --http.api=net,web3,eth \
  --http.corsdomain="*" \
  --http.addr=0.0.0.0 \
  --http.vhosts="*"
```
{% endcode %}

#### **HPP Sepolia (Testnet)**

1. generate EigenDA Proxy environment

```bash
# EigenDA Proxy - .env
EIGENDA_PROXY_EIGENDA_DISPERSER_RPC=disperser-testnet-sepolia.eigenda.xyz:443
EIGENDA_PROXY_EIGENDA_STATUS_QUERY_INTERVAL=5s
EIGENDA_PROXY_EIGENDA_STATUS_QUERY_TIMEOUT=2400s
EIGENDA_PROXY_EIGENDA_ETH_RPC=https://ethereum-sepolia-rpc.publicnode.com
EIGENDA_PROXY_EIGENDA_SERVICE_MANAGER_ADDR=0x3a5acf46ba6890B8536420F4900AC9BC45Df4764

```

2. generate docker-compose.yml

```yaml
version: '3.8'
services:
  eigenda-proxy:
    image: ghcr.io/layr-labs/eigenda-proxy:v1.8.2
    ports:
      - "3100:3100"
    env_file:
      - .env
    command: [
      "--addr", "0.0.0.0",
      "--port", "3100",
      "--api-enabled", "admin"
    ]
```

3. run EigenDA Proxy

```bash
docker compose -p eigenda-proxy up -d

# status
curl http://localhost:3100/admin/eigenda-dispersal-backend
{"eigenDADispersalBackend":"V1"}
```

4. generate and edit hpp-sepolia-node-config.json \[or [download](https://raw.githubusercontent.com/hpp-io/hpp-docs/refs/heads/main/.gitbook/assets/hpp-sepolia-node-config.json)]

```json
{
  "chain": {
    "info-json": "[{\"chain-id\":181228,\"parent-chain-id\":11155111,\"chain-name\":\"conduit-orbit-deployer\",\"chain-config\":{\"chainId\":181228,\"homesteadBlock\":0,\"daoForkBlock\":null,\"daoForkSupport\":true,\"eip150Block\":0,\"eip150Hash\":\"0x0000000000000000000000000000000000000000000000000000000000000000\",\"eip155Block\":0,\"eip158Block\":0,\"byzantiumBlock\":0,\"constantinopleBlock\":0,\"petersburgBlock\":0,\"istanbulBlock\":0,\"muirGlacierBlock\":0,\"berlinBlock\":0,\"londonBlock\":0,\"clique\":{\"period\":0,\"epoch\":0},\"arbitrum\":{\"EnableArbOS\":true,\"AllowDebugPrecompiles\":false,\"DataAvailabilityCommittee\":true,\"InitialArbOSVersion\":32,\"InitialChainOwner\":\"0x3324DC1E72Ee0C0D0483503B5d36A592bfC862D9\",\"GenesisBlockNum\":0}},\"rollup\":{\"bridge\":\"0x1DDe0F57E7889B6866505634E58E3057b01dfed0\",\"inbox\":\"0xAAD45a7bF65b43E56767CdE3Ab84A5433c714Afc\",\"sequencer-inbox\":\"0x7A6398deA2adc6fe4A3cfBA3352840bB03e440d3\",\"rollup\":\"0x60b33120F5572608CC33c5C3a40c992987B59Edc\",\"validator-utils\":\"0x9d502DD38E6E7FBdd3b7e964345d544ec37f1D72\",\"validator-wallet-creator\":\"0x684A827456373a0C0379B1C82BA31Ee5E4F88F62\",\"deployed-at\":8539104} }]",
    "name": "conduit-orbit-deployer"
  },
  "parent-chain": {
    "connection": {
      "url": "https://ethereum-sepolia-rpc.publicnode.com"
    },
    "blob-client": {
      "beacon-url": "https://ethereum-sepolia-beacon-api.publicnode.com"
    }
  },
  "node": {
    "feed": {
      "input": {
        "url": "wss://relay-hpp-sepolia-turdrv0107.t.conduit.xyz"
      }
    },
    "batch-poster": {
      "enable-eigenda-failover": true
    },
    "eigen-da": {
      "enable": true,
      "rpc": "http://localhost:3100"
    },
    "data-availability": {
      "enable": true,
      "rest-aggregator": {
          "enable": true,
          "urls": "https://das-hpp-sepolia-turdrv0107.t.conduit.xyz"
      }
    }
  },
  "execution": {
    "forwarding-target": "https://rpc-hpp-sepolia-turdrv0107.t.conduit.xyz"
  },
  "http": {
    "api": "net,web3,eth",
    "corsdomain": "*",
    "addr": "0.0.0.0",
    "vhosts": "*"
  }
}
```

Replace your rpc and node urls :

* parent-chain.connection.url
* parent-chain.blob-client
* node.eigen-da.rpc



5. run EigenDA Nitro Node

{% code lineNumbers="true" %}
```bash
docker run --rm -it \
  --name hpp-node-sepolia \
  -v ./hpp-sepolia-node-config.json:/config/nodeConfig.json \
  -v ./hpp-sepolia:/home/user/.arbitrum \
  -p 8547:8547 -p 8548:8548 \
  ghcr.io/layr-labs/nitro/nitro-node:v3.5.7 \
  --conf.file /config/nodeConfig.json
```
{% endcode %}

HPP generates blocks only when there are transactions to process. So if you don't see new blocks coming in, it just means there's no transaction activity - your node is still syncing correctly.
