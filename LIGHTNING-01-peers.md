[ [index](/README.md), [previous](/LIGHTNING-00-install.md), [next](/LIGHTNING-02-connect.md) ]

# Lightning nodes Setup

Once we installed all components and we have Litecoin and Bitcoin synced with their blockchains we can set up `lnd` processes. We are going to setup two processes that mimic two exchanges. We suggest to run each `lnd` process in its own terminal and use another terminal with lncli. It will take the `lnd` processes some time to sync with the `btcd` and `ltcd` daemons. It is OK to setup the two `lnd`s in parallel.

## Exchange A

Open a terminal to set Exchange A's `lnd` daemon

### Launch `lnd`
Create a separate directory and launch `lnd` for Exchange A that uses both `Bitcoin` and `Litecoin` chains.
Note that `--debughtlc` is currently mandatory for the success of a swap 
```shell
$ mkdir -p $HOME/exchange-a
$ cd $HOME/exchange-a
$ lnd --noencryptwallet --debughtlc --rpcport=10001 --peerport=10011 --restport=8001 --datadir=data --logdir=logs --debuglevel=debug --nobootstrap --no-macaroons --bitcoin.active --bitcoin.testnet --litecoin.active --litecoin.testnet --bitcoin.rpcuser=xu --bitcoin.rpcpass=xu --litecoin.rpcuser=xu --litecoin.rpcpass=xu
```

## Exchange B

Open another terminal to set Exchange B's `lnd` deamon

### Launch `lnd`
Create a separate directory and launch `lnd` for Exchange B that uses both `Bitcoin` and `Litecoin` chains. Keep in mind that `--debughtlc` is currently mandatory for the Poc.
```shell
$ mkdir -p $HOME/exchange-b
$ cd $HOME/exchange-b
$ lnd --noencryptwallet --debughtlc --rpcport=10002 --peerport=10012 --restport=8002 --datadir=data --logdir=logs --debuglevel=debug --nobootstrap --no-macaroons --bitcoin.active --bitcoin.testnet --litecoin.active --litecoin.testnet --bitcoin.rpcuser=xu --bitcoin.rpcpass=xu --litecoin.rpcuser=xu --litecoin.rpcpass=xu
```
## Wait until Exchange A and Exchange B sync

Give the two `lnd`s the time they need to sync with `btcd` and `ltcd`. You can check the status by using the `getinfo` command (use the cli terminal for this). You would want to see `"synced_to_chain": true,` for both exchanges.

## Check status 

Check status of Exchange A

```shell
$ lncli --rpcserver=localhost:10001 --no-macaroons getinfo
{
        "identity_pubkey": "026374581ff7974975ffce20e65a04876ba33405502d1a13dc73c9a702b61aef31",
        "alias": "",
        "num_pending_channels": 0,
        "num_active_channels": 0,
        "num_peers": 0,
        "block_height": 1318976,
        "block_hash": "0000000000000edb9491ddb942b9afcfee44ddc8dc3fdab293701307483b1771",
        "synced_to_chain": true,
        "testnet": true,
        "chains": [
                "litecoin",
                "bitcoin"
        ]
}
```

Check status of Exchange B

```shell
$ lncli --rpcserver=localhost:10002 --no-macaroons getinfo
{
    "identity_pubkey": "0248a05db7c3996df2699fca9a9a1f843c723b50a6178e805416150b199b5c44bc",
    "alias": "",
    "num_pending_channels": 0,
    "num_active_channels": 0,
    "num_peers": 0,
    "block_height": 1256431,
    "block_hash": "000000000000015571221c48ec7e6a48ddbaeba29d38da5b40fb06639677a4ab",
    "synced_to_chain": true,
    "testnet": true,
    "chains": [
	"litecoin",
	"bitcoin"
    ]
}
```

# Fund Exchange A by using faucets 

## balance after creating

Query Exchange A wallet balances for both `Bitcoin` and `Litecoin` after creation (we expect to see zeros, right?)
```shell
$ lncli --rpcserver=localhost:10001 --no-macaroons walletbalance --ticker=BTC
{
    "balance": "0"
}
$ lncli --rpcserver=localhost:10001 --no-macaroons walletbalance --ticker=LTC
{
    "balance": "0"
}
```

## Create BTC and LTC addresses for deposit

Create Exchange A Segwit addresses for both `Bitcoin` and `Litecoin`
```shell
$ lncli --rpcserver=localhost:10001 --no-macaroons newaddress np2wkh --ticker=BTC
{
        "address": "2NBRavXuXmbd73tRvgCAHhTiSuPoa3LqKdd"
}
$ lncli --rpcserver=localhost:10001 --no-macaroons newaddress np2wkh --ticker=LTC
{
        "address": "2N2yE6ZbdxePtco8DuTSQVEJNsNg74KvGd3"
}
```

## Use addresses to fund Exchange A's wallets

Send some BTC (0.2 or more is great) and some LTC (10 is great) to Exchange A's addresses via testnet faucets (see [README.bitcoin](README.bitcoin.md/#bitcoin-testnet-faucet) and [README.litecoin](README.litecoin.md/#litecoin-testnet-faucet)). Balances should appear in the wallet once the BTC/LTC transactions are mined.

## balance after funding the wallets

Query Exchange A wallet balances for both `Bitcoin` and `Litecoin` after funding
```shell
$ lncli --rpcserver=localhost:10001 --no-macaroons walletbalance --ticker=BTC
{
        "balance": "32500000"
}
$ lncli --rpcserver=localhost:10001 --no-macaroons walletbalance --ticker=LTC
{
        "balance": "1000000000"
}
```



We are now ready with Exchange A's wallets. Note that Exchange B has LTC and BTC wallets but these are empty (zero balance). There is no need to fund Exchange B for our PoC. 

We are now ready to move on to the next step and create the P2P `lnd` network.

[ [index](/README.md), [previous](/LIGHTNING-00-install.md), [next](/LIGHTNING-02-connect.md) ]
