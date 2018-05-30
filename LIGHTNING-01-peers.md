# Lightning Peer Setup

Once we installed all components and we have Litecoin and Bitcoin synced with their blockchains we can set up `lnd` process. We are going to setup two processes that mimic two exchanges. We suggest that you run each lnd process in its own terminal and use another terminal with lncli 

## Exchange A

#### Launch `lnd`
Create a separate directory and launch `lnd` for Exchange A that uses both `Bitcoin` and `Litecoin` chains.
Note that --debughtlc is currently mandatory for the success of a swap 
```shell
$ mkdir -p $HOME/exchange-a
$ cd $HOME/exchange-a
$ lnd --debughtlc --rpcport=10001 --peerport=10011 --restport=8001 --datadir=data --logdir=logs --debuglevel=info --nobootstrap --no-macaroons --bitcoin.active --bitcoin.testnet --litecoin.active --litecoin.testnet  --noencryptwallet
```
Give `lnd` the time it needs to sync with `btcd` and `ltcd`

#### Check status and Create Addresses

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

Send Send some BTC (B0.325 is great) and some LTC (10 is great) to Exchange A addresses via testnet faucets (see [README.bitcoin](README.bitcoin.md/#bitcoin-testnet-faucet) and [README.litecoin](README.litecoin.md/#litecoin-testnet-faucet))
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

We are now ready with Exchange A deamon. 

## Exchange B

Open another terminal to set Exchange B `lnd` deamon

#### Launch `lnd`
Create a separate directory and launch `lnd` for Exchange B that uses both `Bitcoin` and `Litecoin` chains. Keep in mind that `--debughtlc` is currently mandatory for the Poc.
```shell
$ mkdir -p $HOME/exchange-b
$ cd $HOME/exchange-b
$ lnd --noencryptwallet --debughtlc --rpcport=10002 --peerport=10012 --restport=8002 --datadir=data --logdir=logs --debuglevel=info --nobootstrap --no-macaroons --bitcoin.active --bitcoin.testnet --litecoin.active --litecoin.testnet
```
Give `lnd` the time it needs to sync with `btcd` and `ltcd`

#### Check staus 

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

Query Exchange B wallet balances for both `Bitcoin` and `Litecoin` after creation
```shell
$ lncli --rpcserver=localhost:10002 --no-macaroons walletbalance --ticker=BTC
{
    "balance": "0"
}
$ lncli --rpcserver=localhost:10002 --no-macaroons walletbalance --ticker=LTC
{
    "balance": "0"
}
```

If we need to fund Exchange B we can use the same instructions we used for Exchange A. This however is not mandatory for the PoC.

We are now ready to move to the next step and create P2P `lnd` network.
