[ [index](/README.md), [next](/LIGHTNING-01-peers.md) ]

# Lightning
This guide follows [Lightning Installation Guide](https://github.com/lightningnetwork/lnd/blob/master/docs/INSTALL.md) to build the dependencies (`Go`), currency daemons (`btcd` and `ltcd`) and the actual `lnd` daemon.  

## Install Lightning Dependencies
Download latest (`Go`) package from [offical Go repository](https://golang.org/dl/) and uncompress to `/usr/local` .
Alternativly you can use apt-get
```shell
$ sudo apt-get install golang-1.10-go
```

Add the following lines to the end of `$HOME/.bashrc` and source the file 
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

Since the official `lnd` does not support cross chain swaps as of `lightningnetwork/lnd@6b0f984e3155adf8520d050f9b44f694fe099889` we will use instead an xchain-swap enabled experimental `lnd` daemon. 

#### Cross-chain swap enabled `lnd`
To build and install xchain-swap enabled experimental `lnd` daemon referenced [here](https://blog.lightning.engineering/announcement/2017/11/16/ln-swap.html)
```shell
$ git clone -b swapz https://github.com/ExchangeUnion/lnd.git $GOPATH/src/github.com/lightningnetwork/lnd
$ cd $GOPATH/src/github.com/lightningnetwork/lnd
$ glide install
```
Before building the binaries we would need to make a small change to roasbeef\btcd package to allow it to run under newer versions of (`Go`)
```shell
sed '/json.Unmarshal(msg/i\
in.rawResponse = &rawResponse{}\
in.rawNotification = &rawNotification{}\
' $GOPATH/src/github.com/lightningnetwork/lnd/vendor/github.com/roasbeef/btcd/rpcclient/infrastructure.go > /tmp/tmpfile.go
mv /tmp/tmpfile.go $GOPATH/src/github.com/lightningnetwork/lnd/vendor/github.com/roasbeef/btcd/rpcclient/infrastructure.go
```
Finally we can build
```shell
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
$ btcd --testnet --txindex 
```

Sync progress may be tracked as follows
```shell
$ btcctl --testnet getinfo
```

#### for Litecoin
Running the following command will create `rpc.cert`  
Litecoin testnet blockchain is downloaded to `$HOME/.ltcd` by default
```shell
$ ltcd --testnet --txindex 
```

Sync progress may be tracked as follows
```shell
$ ltcctl --testnet  getinfo
```

## Running Lightning Daemon(s)
Once testnet sync is done for Litecoin and Bitcoin deamons, we continue to the next section which explain how to setup two lnd processes for Exchange-A and Exchane-B
