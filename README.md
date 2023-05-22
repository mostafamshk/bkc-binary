# Blockchain Node Setup
This is a walkthrough for setuping and running a node in a **cosmos-sdk**-based blockchain.

**note** : This is a simplified walkthrough. to see full documentation, explore [Official Doc](https://tutorials.cosmos.network/tutorials/9-path-to-prod/#)

## Get started




## 1. Install Binary
At first, you have to build your blockchain project and generate a **binary**. There are several ways, here you can use **Ignite cli** commands. make sure :


1. You have installed **Go** and **Ignite Cli**

2. You have set the **GOPATH** Envirement variable correctly.


for the node that hosts the project source code, you can run the following command to install the binary in the right path(`root/go/bin`):

**note**: make sure you are in project directory path!

```
ignite chain serve
```
**note** : This command runs the blockchain with one node **locally**. you can stop it after it runned successfully and created the binary.

for other nodes that does not have the project source code, you have to get the binary from the node who has the project source code and put it in the right path in your node(`root/go/bin`).
to do that, first build the project for target platforms that are common with this command:

**note**: make sure you are in project directory path!

```
ignite chain build \
    --release.targets linux:amd64 \
    --release.targets linux:arm64 \
    --release.targets darwin:amd64 \
    --output ./release \
    --release
```

This will create Three zipped files in `/release` directory in your project. pick the version that suits your node os and extract it in the right path in your node(`root/go/bin`).


## 2. Node configuration
For this part There are two considerations :

1.  Is this node suppose to be a validator node or not?

2.  are you adding this node at genesis time or at the time that blockchain network is running already?

dont worry! We will take care of each scenario.

### Initialize node

Beside the different cases, every node should get **initialized** on the chosen server. you can do it by simply run the following command:
```
myProjectd init blockchain-age-1
```
This command creates a directory at root(named by default : `.myProjectd`) that looks like this : 

```
.myProjectd : 
    -config
    -data
    -keyring-test
```

also notice that a default `genesis.json` file is created in config directory.

From now on, different cases may happen. to make it user friendly, we discuss every case separately.

### Non-validator node at genesis time
For this case, simply replace the `genesis.json` with the agreed one that is created by other nodes.

note: you can add some **cold keys**(accounts) in your node and add them in genesis file as a **genesis account** with these commands : 

```
myProjectd keys add james --keyring-backend test
```

```
myProjectd add-genesis-account james 1000000000000ntoken --keyring-backend test
```
this command will update the `genesis.json`. make sure you update your genesis files in all other nodes after this.

### Non-validator node at network running time
For this case, simply replace the `genesis.json` with the agreed one that is created by other nodes and make it read-only.

### validator node at genesis time
For this case, follow the steps:

1. replace the `genesis.json` with the agreed one that is created by other nodes.
2. create at least one key and add it to `genesis.json` as a **genesis account** with these commands.
```
myProjectd keys add james --keyring-backend test
```

```
myProjectd add-genesis-account james 1000000000000ntoken --keyring-backend test
```
this will update `genesis.json`. so update the genesis at least in the seed node with this newly created `genesis.json`


3. create a **gentx** file that contains the transaction that handles the **self-delegation** concept for recently created account with this command :
    first, get node **tendermint consensus key**:
```
myProjectd tendermint show-validator
```
    then run this command with your account key, stake amount, and tendermint pubkey values :
```
myprojectd gentx james \
    100000000000ntoken \
    --pubkey '{"@type":"/cosmos.crypto.ed25519.PubKey","key":"byefX/uKpgTsyrcAZKrmYYoFiXG0tmTOOaJFziO3D+E="}' \
    --gas 1000000 \
    --gas-prices 0.1ntoken \
    --keyring-backend test
```

this will create a gentx file in the path `.myprojectd/config/gentx`.
take this file and put it the same directory in the seed node. then run this command :

```
    myProjectd collect-gentxs
```

this will also update `genesis.json` . finally update your node and all other nodes with final `genesis.json` .

### validator node at network running time
This case can be handled like the validator node at genesis time. at least official doc says that!


## 3. Node connection configuration to other nodes
for this part, you have to edit the `/config/config.toml` 
first update the **external_address** field with your node ip like this :
```
    external_address = "172.217.22.14:26656" # replace by your own
```
second, find out your **node id** by running this :
```
    myProjectd tendermint show-node-id
```
third, set the **seeds** field to especify the seed node in your network(with format of : **nodeID@nodeIp:port**) :
```
    seeds = "432d816d0a1648c5bc3f060bd28dea6ff13cb413@216.58.206.174:26656"
```
fourth, set the **persistent_peers** field. this field should contain the nodes identifiers that this node wants to connect with. 
```
persistent_peers = "432d816d0a1648c5bc3f060bd28dea6ff13cb413@216.58.206.174:26656,
5735836cbaa747e013e47b11839db2c2990b918a@121.37.49.12:26656"
```

## 4. Start the node
simply run :
```
myProjectd start
```
