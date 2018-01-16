# Lightning Peer Setup

## Exchange A

#### Launch `lnd`
Create a separate directory and launch `lnd` for Exchange A that uses both `Bitcoin` and `Litecoin` chains
```shell
$ mkdir -p $HOME/exchange-a
$ cd $HOME/exchange-a
$ lnd --rpcport=10001 --peerport=10011 --restport=8001 --datadir=test_data --logdir=test_log --debuglevel=info --nobootstrap --no-macaroons --bitcoin.active --bitcoin.testnet --bitcoin.rpcuser=kek --bitcoin.rpcpass=kek --litecoin.active --litecoin.testnet --litecoin.rpcuser=kek --litecoin.rpcpass=kek
```

#### Create Wallets and Addresses
Create `Bitcoin` and `Litecoin` wallets for Exchange A
```shell
$ lncli --rpcserver=localhost:10001 --no-macaroons create
Input wallet password:
Confirm wallet password:
```

If wallets are already created then `unlock`
```shell
$ lncli --rpcserver=localhost:10001 --no-macaroons unlock
Input wallet password:
```

Check status of Exchange A
```shell
$ lncli --rpcserver=localhost:10001 --no-macaroons getinfo
{
    "identity_pubkey": "026a2f91860f43b03aff44246652a464e68a678251d6d6e0f24a8c4398b8333aa7",
    "alias": "",
    "num_pending_channels": 0,
    "num_active_channels": 0,
    "num_peers": 0,
    "block_height": 1256361,
    "block_hash": "00000000000000a06c82beee4d9db2e5fcb6745fd363c386aa95f9dbb36edbae",
    "synced_to_chain": true,
    "testnet": true,
    "chains": [
	"bitcoin",
	"litecoin"
    ]
}
```

Create Exchange A Segwit addresses for both `Bitcoin` and `Litecoin`
```shell
$ lncli --rpcserver=localhost:10001 --no-macaroons newaddress np2wkh --ticker=BTC
{
    "address": "2NBNkJEJ2WUvJVPMUfhLiZYYpFVvbqNRa2Q"
}
$ lncli --rpcserver=localhost:10001 --no-macaroons newaddress np2wkh --ticker=LTC
{
    "address": "2NCTyvVDJ45Q6umb73Rc6xbzXPNhy75Csvm"
}
```

Query Exchange A wallet balances for both `Bitcoin` and `Litecoin` after creation
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

Send [1 BTC](https://www.blocktrail.com/tBTC/tx/51b7ed93da1f2290d1efde8c49bcabbb893fff02bc68a0424b44a2b938834eb9) and [0.1 LTC](https://chain.so/tx/LTCTEST/c37668cadc7fcf5b5c7b5fdb311dfa22dacf0e233bc14390b0ccff2a382aa4b6) to Exchange A addresses via testnet faucets (see [README.bitcoin](README.bitcoin.md/#bitcoin-testnet-faucet) and [README.litecoin](README.litecoin.md/#litecoin-testnet-faucet))
Query Exchange A wallet balances for both `Bitcoin` and `Litecoin` after funding
```shell
$ lncli --rpcserver=localhost:10001 --no-macaroons walletbalance --ticker=BTC
{
    "balance": "100000000"
}
$ lncli --rpcserver=localhost:10001 --no-macaroons walletbalance --ticker=LTC
{
    "balance": "10000000"
}
```



## Exchange B

#### Launch `lnd`
Create a separate directory and launch `lnd` for Exchange A that uses both `Bitcoin` and `Litecoin` chains
```shell
$ mkdir -p $HOME/exchange-b
$ cd $HOME/exchange-b
$ lnd --rpcport=10002 --peerport=10012 --restport=8002 --datadir=test_data --logdir=test_log --debuglevel=info --nobootstrap --no-macaroons --bitcoin.active --bitcoin.testnet --bitcoin.rpcuser=kek --bitcoin.rpcpass=kek --litecoin.active --litecoin.testnet --litecoin.rpcuser=kek --litecoin.rpcpass=kek
```

#### Create Wallets and Addresses
Create `Bitcoin` and `Litecoin` wallets for Exchange B
```shell
$ lncli --rpcserver=localhost:10002 --no-macaroons create
Input wallet password:
Confirm wallet password:
```

If wallets are already created then `unlock`
```shell
$ lncli --rpcserver=localhost:10002 --no-macaroons unlock
Input wallet password:
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

Create Segwit addresses for both `Bitcoin` and `Litecoin`
```shell
$ lncli --rpcserver=localhost:10002 --no-macaroons newaddress np2wkh --ticker=BTC
{
    "address": "2MxniWxq22pV9gG66T85RVxaUwPpXneeBqC"
}
$ lncli --rpcserver=localhost:10002 --no-macaroons newaddress np2wkh --ticker=LTC
{
    "address": "2NC2Lw2RXrYkWkjrX6xoSiNLMpv86yGWRoh"
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

Send [1.3 BTC](https://www.blocktrail.com/tBTC/tx/a2fff08b81c87e7199f829a402697889ebba7e8d3ea8a895035250cb39622d35) and [10 LTC](https://chain.so/tx/LTCTEST/ee256273cd1f1d88f3d0c1f23cfc2a6dbafa2739e3b4870bd32b2421e30f44ad) to Exchange B addresses via testnet faucets (see [README.bitcoin](README.bitcoin.md/#bitcoin-testnet-faucet) and [README.litecoin](README.litecoin.md/#litecoin-testnet-faucet))
Query Exchange B wallet balances for both `Bitcoin` and `Litecoin` after funding
```shell
$ lncli --rpcserver=localhost:10002 --no-macaroons walletbalance --ticker=BTC
{
    "balance": "130000000"
}
$ lncli --rpcserver=localhost:10002 --no-macaroons walletbalance --ticker=LTC
{
    "balance": "1000000000"
}

```
