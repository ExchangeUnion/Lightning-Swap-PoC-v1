# Lightning Peer Connection
First Exchange A and Exchange B should connect at network level e.g. becoming peers of each other  
In order to do that `identity_pubkey` variable and `peerport` argument is used  

## Checking Connection State

#### Exchange A pre-connection
```shell
$ lncli --rpcserver=localhost:10001 --no-macaroons getinfo
{
    "identity_pubkey": "026a2f91860f43b03aff44246652a464e68a678251d6d6e0f24a8c4398b8333aa7",
    "alias": "",
    "num_pending_channels": 0,
    "num_active_channels": 0,
    "num_peers": 1,
    "block_height": 1256437,
    "block_hash": "0000000000000678c1b3e9d5c150f585e97091ac3d81e805fb9efc9c1340b0d5",
    "synced_to_chain": true,
    "testnet": true,
    "chains": [
	"litecoin",
	"bitcoin"
    ]
}
$ lncli --rpcserver=localhost:10001 --no-macaroons listpeers
{
    "peers": [
	{
	    "pub_key": "03933884aaf1d6b108397e5efe5c86bcf2d8ca8d2f700eda99db9214fc2712b134",
	    "peer_id": 1,
	    "address": "34.250.234.192:9735",
	    "bytes_sent": "70416",
	    "bytes_recv": "79528",
	    "sat_sent": "0",
	    "sat_recv": "0",
	    "inbound": true,
	    "ping_time": "10194494"
	}
    ]
}
```

#### Exchange B pre-connection
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
$ lncli --rpcserver=localhost:10002 --no-macaroons listpeers
{
    "peers": []
}
```


## Establishing Connection
By using Exchange A's key `identity_pubkey` and port number `peerport`, Exchange B establish connection using the command below
```shell
$ lncli --rpcserver=localhost:10002 --no-macaroons connect 026a2f91860f43b03aff44246652a464e68a678251d6d6e0f24a8c4398b8333aa7@localhost:10011
{
    "peer_id": 0
}
```

#### Exchange A post-connection
```shell
$ lncli --rpcserver=localhost:10001 --no-macaroons listpeers
{
    "peers": [
	{
	    "pub_key": "03933884aaf1d6b108397e5efe5c86bcf2d8ca8d2f700eda99db9214fc2712b134",
	    "peer_id": 1,
	    "address": "34.250.234.192:9735",
	    "bytes_sent": "71937",
	    "bytes_recv": "82799",
	    "sat_sent": "0",
	    "sat_recv": "0",
	    "inbound": true,
	    "ping_time": "7859649"
	},
	{
	    "pub_key": "0248a05db7c3996df2699fca9a9a1f843c723b50a6178e805416150b199b5c44bc",
	    "peer_id": 2,
	    "address": "127.0.0.1:40270",
	    "bytes_sent": "149",
	    "bytes_recv": "7",
	    "sat_sent": "0",
	    "sat_recv": "0",
	    "inbound": false,
	    "ping_time": "0"
	}
    ]
}
```

#### Exchange B post-connection
```shell
$ lncli --rpcserver=localhost:10002 --no-macaroons listpeers
{
    "peers": [
	{
	    "pub_key": "026a2f91860f43b03aff44246652a464e68a678251d6d6e0f24a8c4398b8333aa7",
	    "peer_id": 1,
	    "address": "127.0.0.1:10011",
	    "bytes_sent": "7",
	    "bytes_recv": "149",
	    "sat_sent": "0",
	    "sat_recv": "0",
	    "inbound": true,
	    "ping_time": "0"
	}
    ]
}
```
