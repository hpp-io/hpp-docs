---
hidden: true
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

### Run using Docker

Download and install [Docker](https://www.docker.com/), Docker Compose and ensure it is running.

#### Clone the Repository

```
git clone https://github.com/hpp-io/hpp-node.git
cd hpp-node
```

#### Run a HPP Sepolia node

Using Docker Compose:

```
chainId: 1
...
..
```
