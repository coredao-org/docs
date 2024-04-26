---
description: How to run a full node on Core
---

# Core Full Nodes

A Core full node stores the entire history of the Core blockchain, enabling anyone to verify the state of any account. A Core full node can take many forms:

* **Normal full node**: for private use.
* **Validator full node**: acts as a validator on Core, validating blocks and transactions.
* **RPC full node**: provides RPC services and responds to HTTP requests.

## System Requirements

There are several system requirements for setting up a full node on Core.

### Software

Currently, a Core full node can only run on Mac OS X and Linux.

### Hardware

Core nodes perform several resource-intensive tasks, which can include storing blockchain data, verifying blocks or transactions, communicating with peer nodes, and answering network requests, depending on their configuration. Each type of Core node has specific hardware requirements based on their expected needs.

#### Testnet Full Node Hardware Specifications

For full nodes on **Core testnet**, we recommend the following minimal hardware specs:

| Normal node                                                                            |
| -------------------------------------------------------------------------------------- |
| 1 TB of free disk space.                                                               |
| 4 CPU cores and 8 gigabytes of memory (RAM).                                           |
| A broadband Internet connection with upload/download speeds of 5 megabytes per second. |

| Validator node                                                                                         |
| ------------------------------------------------------------------------------------------------------ |
| 1 TB of free disk space, solid-state drive (SSD), gp3, 8k IOPS, 250MB/S throughput, read latency <1ms. |
| 4 CPU cores and 8 gigabytes of memory (RAM).                                                           |
| A broadband Internet connection with upload/download speeds of 10 megabytes per second.                |

| RPC node                                                                                               |
| ------------------------------------------------------------------------------------------------------ |
| 1 TB of free disk space, solid-state drive (SSD), gp3, 8k IOPS, 250MB/S throughput, read latency <1ms. |
| 8 cores of CPU and 16 gigabytes of memory (RAM).                                                       |
| A broadband Internet connection with upload/download speeds of 50 megabytes per second.                |

#### Mainnet Full Node Hardware Specifications

For full nodes on **Core mainnet**, we recommend the following minimal hardware specs:

| Normal node                                                                            |
| -------------------------------------------------------------------------------------- |
| 1 TB of free disk space.                                                               |
| 4 CPU cores and 32 gigabytes of memory (RAM).                                          |
| A broadband Internet connection with upload/download speeds of 5 megabytes per second. |

| Validator node                                                                                         |
| ------------------------------------------------------------------------------------------------------ |
| 1 TB of free disk space, solid-state drive (SSD), gp3, 8k IOPS, 250MB/S throughput, read latency <1ms. |
| 8 CPU cores and 32 gigabytes of memory (RAM).                                                          |
| A broadband Internet connection with upload/download speeds of 10 megabytes per second.                |

| RPC node                                                                                               |
| ------------------------------------------------------------------------------------------------------ |
| 1 TB of free disk space, solid-state drive (SSD), gp3, 8k IOPS, 250MB/S throughput, read latency <1ms. |
| 16 CPU cores and 32 gigabytes of memory (RAM).                                                         |
| A broadband Internet connection with upload/download speeds of 50 megabytes per second.                |

### Network

If you are running an RPC node, you need to enable `HTTP` inbound rules on port 8575 in the firewall settings. Port 8575 is set as the default port. You can change the configurations in `config.toml`.

## Build and Run

1\. We recommend using the[ core-chain](https://github.com/coredao-org/core-chain) GitHub repository to directly build and run your full node, running your full node directly from our blockchain codebase. Instructions for building the source code can be found in the repository's[ README](https://github.com/coredao-org/core-chain#building-the-source).

2\. Download the node binary from the[ releases page](https://github.com/coredao-org/core-chain/releases) of the core-chain repository. The node binary includes the relevant mainnet and testnet configuration files. This is the [latest version](https://github.com/coredao-org/core-chain/releases/latest).

3\. Write the genesis state locally by executing the following command from your project directory:

```bash
geth --datadir node init genesis.json
```

You should see the following output:

```bash
INFO [07-18|14:57:20.715] Maximum peer count                       ETH=25 LES=0 total=25
INFO [07-18|14:57:20.721] Allocated cache and file handles         database=/Users/jackcrypto/go/core-chain/node/geth/chaindata cache=16 handles=16
INFO [07-18|14:57:20.724] Writing custom genesis block 
INFO [07-18|14:57:20.725] Persisted trie from memory database      nodes=25 size=87.18kB time=226.129µs gcnodes=0 gcsize=0.00B gctime=0s livenodes=1 livesize=0.00B
INFO [07-18|14:57:20.725] Successfully wrote genesis state         database=chaindata                             hash=d90508…5c034a
INFO [07-18|14:57:20.725] Allocated cache and file handles         database=/Users/jackcrypto/go/core-chain/node/geth/lightchaindata cache=16 handles=16
INFO [07-18|14:57:20.729] Writing custom genesis block 
INFO [07-18|14:57:20.729] Persisted trie from memory database      nodes=25 size=87.18kB time=178.332µs gcnodes=0 gcsize=0.00B gctime=0s livenodes=1 livesize=0.00B
INFO [07-18|14:57:20.730] Successfully wrote genesis state         database=lightchaindata                             hash=d90508…5c034a
```

4\. Our full node is ready, let's start running it!

If you plan to run a validator node, you'll need to set up the consensus key before running the node. Make sure to keep your keystore saved.

```bash
# generate the consensus key and input the password
geth account new --datadir ./node
echo {your-password} > password.txt
# start a validator node
geth --config ./config.toml --datadir ./node -unlock {your-validator-address} --password password.txt  --mine  --allow-insecure-unlock  --cache 8000
```

If you plan to run a normal node or an RPC node, you can just run the following `geth` command directly:

```bash
## start a full node
geth --config ./config.toml --datadir ./node  --cache 8000
```

5\. As our full node runs, we can monitor its logs to make sure that everything is operating correctly. The log file is located at `/node/logs/core.log` by default, but can be changed to another location if desired.
