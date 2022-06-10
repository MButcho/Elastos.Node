# Installing Elastos DID Sidechain

Running the following command to install and configure Elastos DID Sidechain.

```bash
$ ~/node/node.sh did init
```

```bash
Finding the latest did release...
INFO: Latest version: v0.3.2
Downloading https://download.elastos.io/elastos-did/elastos-did-v0.3.2/elastos-did-v0.3.2-linux-x86_64.tgz...
###################################################################### 100.0%
Extracting elastos-did-v0.3.2-linux-x86_64.tgz...
'/home/ubuntu/node/.node-upload/did/did' -> '/home/ubuntu/node/did/did'
Creating did config file...
Generating random userpass for did RPC interface...
```

Please enter an address to receive block rewards on the DID sidechain. Here you can enter an address of the ELA main chain copied from Essentials.

```bash
Please input an ELA address to receive awards.
? PayToAddr: ......
```

MinerInfo is the miner's identification, which will be recorded in the block generated by this miner.

```bash
Please input a miner name that will be persisted in the blockchain.
? MinerInfo: The Miner
Updating did config file...
INFO: did config file: /home/ubuntu/node/did/config.json
OK: did initialized
```