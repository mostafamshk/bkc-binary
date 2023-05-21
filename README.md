# Blockchain Node Setup
This is a walkthrough for setuping and running a node in a **cosmos-sdk**-based blockchain.

**note** : This is a simplified walkthrough. to see full documentation, explore [Official Doc](https://tutorials.cosmos.network/tutorials/9-path-to-prod/#)

## Get started

### Install Binary
At first, you have to build your blockchain project and generate a binary. There are several ways, here you can use Ignite cli commands. make sure :


1. You have installed Go and Ignite Cli 

2. You have set the GOPATH Envirement variable correctly.


for the node that hosts the project source code, you can run the following command to install the binary in the right path(root/go/bin):

note: make sure you are in project directory path!

```
ignite chain serve
```
This command runs 

for other nodes that does not have the project source code, you have to get the binary from the node who has the project source code and put it in the right path in your node(root/go/bin).
to do that, first build the project for target platforms that are common with this command:

note: make sure you are in project directory path!

```
ignite chain build \
    --release.targets linux:amd64 \
    --release.targets linux:arm64 \
    --release.targets darwin:amd64 \
    --output ./release \
    --release
```

This will create Three zipped files in /release directory in your project. pick the version that suits your node os and extract it in the right path in your node(root/go/bin).


### Node configuration

