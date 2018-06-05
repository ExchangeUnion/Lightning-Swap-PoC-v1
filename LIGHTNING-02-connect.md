[ [index](/README.md), [previous](/LIGHTNING-01-peers.md), [next](/LIGHTNING-03-channels.md) ]

# Lightning Peer Connection
In this step we connect Exchange A and Exchange B at network level so they become peers of each other   

## LND processes restart
```diff
-Before we set the connection we need to restart the lnd processes of exchange A and B. 
-You can use control+c at the terminal to do that. 
-You may get a lot of warnings `CRTR: Unable to get block hash for block at height`. These can be ignored. 
-If stopping takes long time you can kill the lnd processes using the command `killall lnd` at the cli terminal.
```

Once the lnd processes stopped, please restart them (same commands we used before).

## Checking Connection State

### Exchange A pre-connection
```shell
$ lncli --rpcserver=localhost:10001 --no-macaroons getinfo
{
        "identity_pubkey": "026374581ff7974975ffce20e65a04876ba33405502d1a13dc73c9a702b61aef31",
        "alias": "",
        "num_pending_channels": 0,
        "num_active_channels": 0,
        "num_peers": 0,
        "block_height": 1319006,
        "block_hash": "000000000000045010daa190db0cdd3e6387f308e3ebd4c4d08b3058f7b385fb",
        "synced_to_chain": true,
        "testnet": true,
        "chains": [
                "litecoin",
                "bitcoin"
        ]
}
$ lncli --rpcserver=localhost:10001 --no-macaroons listpeers
{
    "peers": []
}
```

### Exchange B pre-connection
```shell
$ lncli --rpcserver=localhost:10002 --no-macaroons getinfo
{
        "identity_pubkey": "0237cdf6b03cf17df8676af35b43da3ee0613b888bc5cd26a41064118f1241cc2f",
        "alias": "",
        "num_pending_channels": 0,
        "num_active_channels": 0,
        "num_peers": 0,
        "block_height": 1319006,
        "block_hash": "000000000000045010daa190db0cdd3e6387f308e3ebd4c4d08b3058f7b385fb",
        "synced_to_chain": true,
        "testnet": true,
        "chains": [
                "litecoin",
                "bitcoin"
        ]
}
$ lncli --rpcserver=localhost:10002 --no-macaroons listpeers
{
    "peers": []
}
```


## Establishing Connection
By using Exchange A's `identity_pubkey`, host and port number, Exchange B establishes a connection using the following command
```shell
$ X_A_ID_PUBKEY=`lncli --rpcserver=localhost:10001 --no-macaroons getinfo|grep identity_pubkey|cut -d '"' -f 4`

$ lncli --rpcserver=localhost:10002 --no-macaroons connect $X_A_ID_PUBKEY@127.0.0.1:10011
{
    "peer_id": 0
}
```

### Exchange A post-connection
```shell
$ lncli --rpcserver=localhost:10001 --no-macaroons listpeers
{
        "peers": [
                {
                        "pub_key": "0237cdf6b03cf17df8676af35b43da3ee0613b888bc5cd26a41064118f1241cc2f",
                        "peer_id": 1,
                        "address": "127.0.0.1:56624",
                        "bytes_sent": "149",
                        "bytes_recv": "149",
                        "sat_sent": "0",
                        "sat_recv": "0",
                        "inbound": false,
                        "ping_time": "0"
                }
        ]
}
```

### Exchange B post-connection
```shell
$ lncli --rpcserver=localhost:10002 --no-macaroons listpeers
{
        "peers": [
                {
                        "pub_key": "026374581ff7974975ffce20e65a04876ba33405502d1a13dc73c9a702b61aef31",
                        "peer_id": 1,
                        "address": "127.0.0.1:10011",
                        "bytes_sent": "149",
                        "bytes_recv": "149",
                        "sat_sent": "0",
                        "sat_recv": "0",
                        "inbound": true,
                        "ping_time": "0"
                }
        ]
}
```

We are no ready to set up payment channels. 

[ [index](/README.md), [previous](/LIGHTNING-01-peers.md), [next](/LIGHTNING-03-channels.md) ]
