# Lightning
[Lightning Installation Guide](https://github.com/lightningnetwork/lnd/blob/master/docs/INSTALL.md) is used to build the dependencies (`Go`), currency daemons (`btcd` and `ltcd`) and the actual `lnd` daemon.  

## Install Lightning Dependencies
Download latest package from [offical Go repository](https://golang.org/dl/) and uncompress to `/usr/local`
```shell
$ sudo tar -C /usr/local -xzf go1.9.2.linux-amd64.tar.gz
```

Add the following lines to the end of `$HOME/.bashrc`
```shell
# add Go paths
export GOPATH=$HOME/go
export PATH=/usr/local/go/bin:$GOPATH/bin:$PATH
```

Install `Glide`
```shell
$ go get -u github.com/Masterminds/glide
```

## Build Lightning Daemon

#### Official `lnd`
Build and install official `lnd` daemon
```shell
$ git clone https://github.com/lightningnetwork/lnd $GOPATH/src/github.com/lightningnetwork/lnd
$ cd $GOPATH/src/github.com/lightningnetwork/lnd
$ glide install
$ go install . ./cmd/...
```

However official `lnd` does not support cross chain swaps as of `lightningnetwork/lnd@6b0f984e3155adf8520d050f9b44f694fe099889`
```shell
$ lnd --debuglevel=debug --bitcoin.active --bitcoin.testnet --bitcoin.rpcuser=kek --bitcoin.rpcpass=kek --litecoin.active --litecoin.testnet --litecoin.rpcuser=kek --litecoin.rpcpass=kek
loadConfig: Currently both Bitcoin and Litecoin cannot be active together
```

#### Cross-chain swap enabled `lnd`
To build and install xchain-swap enabled experimental `lnd` daemon referenced [here](https://blog.lightning.engineering/announcement/2017/11/16/ln-swap.html)
```shell
$ git clone -b swapz https://github.com/cfromknecht/lnd.git $GOPATH/src/github.com/lightningnetwork/lnd
$ cd $GOPATH/src/github.com/lightningnetwork/lnd
$ glide install
$ go install . ./cmd/...
```

#### Bitcoin full node implementation `btcd`
Build and install `btcd` Bitcoin full node implementation
```shell
$ git clone https://github.com/roasbeef/btcd $GOPATH/src/github.com/roasbeef/btcd
$ cd $GOPATH/src/github.com/roasbeef/btcd
$ glide install
$ go install . ./cmd/...
```

#### Litecoin full node implementation `ltcd`
Build and install `ltcd` Litecoin full node implementation
```shell
$ git clone https://github.com/ltcsuite/ltcd.git $GOPATH/src/github.com/ltcsuite/ltcd
$ cd $GOPATH/src/github.com/ltcsuite/ltcd
$ glide install
$ go install . ./cmd/...
```

## Setup Blockchain Clients

#### for Bitcoin
Running the following command will create `rpc.cert`  
Bitcoin testnet blockchain is downloaded to `$HOME/.btcd` by default
```shell
$ btcd --testnet --txindex --rpcuser=kek --rpcpass=kek
```

Sync progress may be tracked as follows
```shell
$ btcctl --testnet --rpcuser=kek --rpcpass=kek getinfo
```

#### for Litecoin
Running the following command will create `rpc.cert`  
Litecoin testnet blockchain is downloaded to `$HOME/.ltcd` by default
```shell
$ ltcd --testnet --txindex --rpcuser=kek --rpcpass=kek
```

Sync progress may be tracked as follows
```shell
$ ltcctl --testnet --rpcuser=kek --rpcpass=kek getinfo
```

## Running Lightning Daemon
After testnet sync is done, following command can be use to launch `lnd` for testing  
Lightning data is stored in `$HOME/.lnd` by default
```shell
$ lnd --debuglevel=debug --bitcoin.active --bitcoin.testnet --bitcoin.rpcuser=kek --bitcoin.rpcpass=kek --litecoin.active --litecoin.testnet --litecoin.rpcuser=kek --litecoin.rpcpass=kek
```
