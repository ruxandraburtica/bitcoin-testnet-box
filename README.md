# Bitcoin testnet box
[![docker pulls](https://img.shields.io/docker/pulls/freewil/bitcoin-testnet-box.svg?style=flat)](https://hub.docker.com/r/freewil/bitcoin-testnet-box/)

Creating your own private bitcoin testnet.

The commands following can be ran within a [Docker](https://www.docker.com) container that is started using the following:
```
docker run -it -p 19001:19001 -p 19011:19011 freewil/bitcoin-testnet-box
```

## Starting the nodes

This will start up two nodes using the two datadirs `1` and `2`. They
will only connect to each other in order to remain an isolated private testnet.
Two nodes are provided, as one is used to generate blocks and it's balance
will be increased as this occurs (imitating a miner). You may want a second node
where this behavior is not observed.

Node `1` will listen on port `19000`, allowing node `2` to connect to it.

Node `1` will listen on port `19001` and node `2` will listen on port `19011`
for the JSON-RPC server.

```
bitcoind -datadir=1 -daemon
bitcoind -datadir=2 -daemon
```

### Check the status of the nodes

```
$ bitcoin-cli -datadir=1 getinfo
{
  "version": 130200,
  "protocolversion": 70015,
  "walletversion": 130000,
  "balance": 0.00000000,
  "blocks": 0,
  "timeoffset": 0,
  "connections": 1,
  "proxy": "",
  "difficulty": 4.656542373906925e-10,
  "testnet": false,
  "keypoololdest": 1496178575,
  "keypoolsize": 100,
  "paytxfee": 0.00000000,
  "relayfee": 0.00001000,
  "errors": ""
}
$ bitcoin-cli -datadir=2  getinfo
{
  "version": 130200,
  "protocolversion": 70015,
  "walletversion": 130000,
  "balance": 0.00000000,
  "blocks": 0,
  "timeoffset": 0,
  "connections": 1,
  "proxy": "",
  "difficulty": 4.656542373906925e-10,
  "testnet": false,
  "keypoololdest": 1496178581,
  "keypoolsize": 100,
  "paytxfee": 0.00000000,
  "relayfee": 0.00001000,
  "errors": ""
}
```

### Generating blocks

Normally on the live, real, bitcoin network, blocks are generated, on average,
every 10 minutes. Since this testnet-in-box uses Bitcoin Core's (bitcoind)
regtest mode, we are able to generate a block on a private network
instantly using a simple command.

To generate a block:

```
bitcoin-cli -datadir=1 generate 1
```


### Need to generate at least 100 blocks before there will be a balance in the first wallet
```
bitcoin-cli -datadir=1 generate 200
```

### Verify that there is a balance on the first wallet
```
bitcoin-cli -datadir=1 getinfo
```

### Generate a wallet address for the second wallet
```
bitcoin-cli -datadir=2 getnewaddress
```

### Sending bitcoins
To send bitcoins that you've generated to the second wallet: (be sure to change the ADDRESS value below to wallet address generated in the prior command)

```
bitcoin-cli -datadir=1 sendtoaddress "mzXCmhrMGqN8gJSrpQ4wXmA9fJ1DSzpYAz" 10
```

### Does the balance show up?
Run the getinfo command again. Does the balance show up? Why not?
```
bitcoin-cli -datadir=2 getinfo
```

How about running the `getwalletinfo` command?
```
bitcoin-cli -datadir=2 getwalletinfo
```

### Showing information about your wallets
```
bitcoin-cli -datadir=2 listreceivedbyaddress 0 true
```

#### More
For more details, you could run the help command on the `bitcoin-cli`:
```
bitcoin-cli -datadir=2 help
```


## Stopping the testnet-box
Just exit the docker container and remove it afterwards.
