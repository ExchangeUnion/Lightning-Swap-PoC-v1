# Lightning Payment

## Bitcoin Payment
Exchange B creates invoice for `1000 Satoshi`
```shell
$ lncli --rpcserver=localhost:10002 --no-macaroons addinvoice --value=1000 --ticker=BTC
{
    "r_hash": "ded910abaaf18047541bcee8d58e24da98f9ef87c124a993904da99d891c5cbf",
    "pay_req": "lntb10u1pdytm2qpp5mmv3p2a27xqyw4qmem5dtr3ym2v0nmu8cyj2nyusfk5emzgutjlsdqqcqzysw345mnfc55vrqkjpj6rw3227wpnsm5lj6ujaaq8z0rvlzzluevjhd2xdx8gcrcundxpqa698ej6akr0ptem8jacuekkxwdzd85hf2wspyrfnwn"
}
```

Exchange A pays `1000 Satoshi` using encoded payment request
```shell
$ lncli --rpcserver=localhost:10001 --no-macaroons sendpayment --pay_req=lntb10u1pdytm2qpp5mmv3p2a27xqyw4qmem5dtr3ym2v0nmu8cyj2nyusfk5emzgutjlsdqqcqzysw345mnfc55vrqkjpj6rw3227wpnsm5lj6ujaaq8z0rvlzzluevjhd2xdx8gcrcundxpqa698ej6akr0ptem8jacuekkxwdzd85hf2wspyrfnwn
{
    "payment_error": "",
    "payment_preimage": "2e2384405a8f6771c30f7b560c62914b07ef7e9f5ce42772a1da6facc92dd551",
    "payment_route": {
	"total_time_lock": 1256822,
	"total_amt": 1000,
	"hops": [
	    {
		"chan_id": 1381475887161802752,
		"chan_capacity": 16000000,
		"amt_to_forward": 1000,
		"expiry": 1256822
	    }
	]
    }
}
```

#### Exchange A's Balances & Channel Status Pre Payment
```shell
$ lncli --rpcserver=localhost:10001 --no-macaroons walletbalance --ticker=BTC
{
    "balance": "83991564"
}
$ lncli --rpcserver=localhost:10001 --no-macaroons walletbalance --ticker=LTC
{
    "balance": "10000000"
}
$ lncli --rpcserver=localhost:10001 --no-macaroons listchannels
{
    "channels": [
	{
	    "active": true,
	    "remote_pubkey": "0248a05db7c3996df2699fca9a9a1f843c723b50a6178e805416150b199b5c44bc",
	    "channel_point": "1051ea63b1928714c8e319eeab0abb2fb639ea7c007315f26383132c500fe077:0",
	    "chan_id": "1381475887161802752",
	    "capacity": "16000000",
	    "local_balance": "15991312",
	    "remote_balance": "0",
	    "commit_fee": "8688",
	    "commit_weight": "600",
	    "fee_per_kw": "12000",
	    "unsettled_balance": "0",
	    "total_satoshis_sent": "0",
	    "total_satoshis_received": "0",
	    "num_updates": "26",
	    "pending_htlcs": []
	},
	{
	    "active": true,
	    "remote_pubkey": "0248a05db7c3996df2699fca9a9a1f843c723b50a6178e805416150b199b5c44bc",
	    "channel_point": "70b9250e7ebf4a069823d884d1fb7d23fe4a02e27f96a6905a6857724bf3f1f4:0",
	    "chan_id": "333714973170008064",
	    "capacity": "10000000",
	    "local_balance": "0",
	    "remote_balance": "9963800",
	    "commit_fee": "36200",
	    "commit_weight": "552",
	    "fee_per_kw": "50000",
	    "unsettled_balance": "0",
	    "total_satoshis_sent": "0",
	    "total_satoshis_received": "0",
	    "num_updates": "0",
	    "pending_htlcs": []
	}
    ]
}
```

#### Exchange A's Balances & Channel Status Post Payment
```shell
$ lncli --rpcserver=localhost:10001 --no-macaroons walletbalance --ticker=BTC{
    "balance": "83991564"
}
$ lncli --rpcserver=localhost:10001 --no-macaroons walletbalance --ticker=LTC
{
    "balance": "10000000"
}
$ lncli --rpcserver=localhost:10001 --no-macaroons listchannels
{
    "channels": [
	{
	    "active": true,
	    "remote_pubkey": "0248a05db7c3996df2699fca9a9a1f843c723b50a6178e805416150b199b5c44bc",
	    "channel_point": "1051ea63b1928714c8e319eeab0abb2fb639ea7c007315f26383132c500fe077:0",
	    "chan_id": "1381475887161802752",
	    "capacity": "16000000",
	    "local_balance": "15990312",
	    "remote_balance": "1000",
	    "commit_fee": "8688",
	    "commit_weight": "724",
	    "fee_per_kw": "12000",
	    "unsettled_balance": "0",
	    "total_satoshis_sent": "1000",
	    "total_satoshis_received": "0",
	    "num_updates": "28",
	    "pending_htlcs": []
	},
	{
	    "active": true,
	    "remote_pubkey": "0248a05db7c3996df2699fca9a9a1f843c723b50a6178e805416150b199b5c44bc",
	    "channel_point": "70b9250e7ebf4a069823d884d1fb7d23fe4a02e27f96a6905a6857724bf3f1f4:0",
	    "chan_id": "333714973170008064",
	    "capacity": "10000000",
	    "local_balance": "0",
	    "remote_balance": "9963800",
	    "commit_fee": "36200",
	    "commit_weight": "552",
	    "fee_per_kw": "50000",
	    "unsettled_balance": "0",
	    "total_satoshis_sent": "0",
	    "total_satoshis_received": "0",
	    "num_updates": "0",
	    "pending_htlcs": []
	}
    ]
}
```

#### Exchange B's Balances & Channel Status Pre Payment
```shell
$ lncli --rpcserver=localhost:10002 --no-macaroons walletbalance --ticker=BTC
{
    "balance": "130000000"
}
$ lncli --rpcserver=localhost:10002 --no-macaroons walletbalance --ticker=LTC
{
    "balance": "989964850"
}
$ lncli --rpcserver=localhost:10002 --no-macaroons listchannels
{
    "channels": [
	{
	    "active": true,
	    "remote_pubkey": "026a2f91860f43b03aff44246652a464e68a678251d6d6e0f24a8c4398b8333aa7",
	    "channel_point": "1051ea63b1928714c8e319eeab0abb2fb639ea7c007315f26383132c500fe077:0",
	    "chan_id": "1381475887161802752",
	    "capacity": "16000000",
	    "local_balance": "0",
	    "remote_balance": "15991312",
	    "commit_fee": "8688",
	    "commit_weight": "552",
	    "fee_per_kw": "12000",
	    "unsettled_balance": "0",
	    "total_satoshis_sent": "0",
	    "total_satoshis_received": "0",
	    "num_updates": "26",
	    "pending_htlcs": []
	},
	{
	    "active": true,
	    "remote_pubkey": "026a2f91860f43b03aff44246652a464e68a678251d6d6e0f24a8c4398b8333aa7",
	    "channel_point": "70b9250e7ebf4a069823d884d1fb7d23fe4a02e27f96a6905a6857724bf3f1f4:0",
	    "chan_id": "333714973170008064",
	    "capacity": "10000000",
	    "local_balance": "9963800",
	    "remote_balance": "0",
	    "commit_fee": "36200",
	    "commit_weight": "600",
	    "fee_per_kw": "50000",
	    "unsettled_balance": "0",
	    "total_satoshis_sent": "0",
	    "total_satoshis_received": "0",
	    "num_updates": "0",
	    "pending_htlcs": []
	}
    ]
}
```

#### Exchange B's Balances & Channel Status Post Payment
```shell
$ lncli --rpcserver=localhost:10002 --no-macaroons walletbalance --ticker=BTC
{
    "balance": "130000000"
}
$ lncli --rpcserver=localhost:10002 --no-macaroons walletbalance --ticker=LTC
{
    "balance": "989964850"
}
$ lncli --rpcserver=localhost:10002 --no-macaroons listchannels
{
    "channels": [
	{
	    "active": true,
	    "remote_pubkey": "026a2f91860f43b03aff44246652a464e68a678251d6d6e0f24a8c4398b8333aa7",
	    "channel_point": "1051ea63b1928714c8e319eeab0abb2fb639ea7c007315f26383132c500fe077:0",
	    "chan_id": "1381475887161802752",
	    "capacity": "16000000",
	    "local_balance": "1000",
	    "remote_balance": "15990312",
	    "commit_fee": "8688",
	    "commit_weight": "724",
	    "fee_per_kw": "12000",
	    "unsettled_balance": "0",
	    "total_satoshis_sent": "0",
	    "total_satoshis_received": "1000",
	    "num_updates": "28",
	    "pending_htlcs": []
	},
	{
	    "active": true,
	    "remote_pubkey": "026a2f91860f43b03aff44246652a464e68a678251d6d6e0f24a8c4398b8333aa7",
	    "channel_point": "70b9250e7ebf4a069823d884d1fb7d23fe4a02e27f96a6905a6857724bf3f1f4:0",
	    "chan_id": "333714973170008064",
	    "capacity": "10000000",
	    "local_balance": "9963800",
	    "remote_balance": "0",
	    "commit_fee": "36200",
	    "commit_weight": "600",
	    "fee_per_kw": "50000",
	    "unsettled_balance": "0",
	    "total_satoshis_sent": "0",
	    "total_satoshis_received": "0",
	    "num_updates": "0",
	    "pending_htlcs": []
	}
    ]
}
```


## Litecoin Payment
Exchange A creates invoice for `5000 Litoshi`
```shell
$ lncli --rpcserver=localhost:10001 --no-macaroons getinfo
{
    "identity_pubkey": "026a2f91860f43b03aff44246652a464e68a678251d6d6e0f24a8c4398b8333aa7",
    "alias": "",
    "num_pending_channels": 0,
    "num_active_channels": 2,
    "num_peers": 1,
    "block_height": 1256780,
    "block_hash": "00000000e1b4c3539106670d90e5c6cc02416cff67c58cf65a31aa69024aaa9d",
    "synced_to_chain": true,
    "testnet": true,
    "chains": [
	"litecoin",
	"bitcoin"
    ]
}
$ lncli --rpcserver=localhost:10001 --no-macaroons walletbalance --ticker=BTC
{
    "balance": "83991564"
}
$ lncli --rpcserver=localhost:10001 --no-macaroons walletbalance --ticker=LTC
{
    "balance": "10000000"
}
$ lncli --rpcserver=localhost:10001 --no-macaroons listpeers
{
    "peers": [
	{
	    "pub_key": "0248a05db7c3996df2699fca9a9a1f843c723b50a6178e805416150b199b5c44bc",
	    "peer_id": 1,
	    "address": "127.0.0.1:37576",
	    "bytes_sent": "290569",
	    "bytes_recv": "291693",
	    "sat_sent": "1000",
	    "sat_recv": "0",
	    "inbound": false,
	    "ping_time": "304"
	}
    ]
}
$ lncli --rpcserver=localhost:10001 --no-macaroons listchannels
{
    "channels": [
	{
	    "active": true,
	    "remote_pubkey": "0248a05db7c3996df2699fca9a9a1f843c723b50a6178e805416150b199b5c44bc",
	    "channel_point": "1051ea63b1928714c8e319eeab0abb2fb639ea7c007315f26383132c500fe077:0",
	    "chan_id": "1381475887161802752",
	    "capacity": "16000000",
	    "local_balance": "15990312",
	    "remote_balance": "1000",
	    "commit_fee": "8688",
	    "commit_weight": "724",
	    "fee_per_kw": "12000",
	    "unsettled_balance": "0",
	    "total_satoshis_sent": "1000",
	    "total_satoshis_received": "0",
	    "num_updates": "30",
	    "pending_htlcs": []
	},
	{
	    "active": true,
	    "remote_pubkey": "0248a05db7c3996df2699fca9a9a1f843c723b50a6178e805416150b199b5c44bc",
	    "channel_point": "70b9250e7ebf4a069823d884d1fb7d23fe4a02e27f96a6905a6857724bf3f1f4:0",
	    "chan_id": "333714973170008064",
	    "capacity": "10000000",
	    "local_balance": "0",
	    "remote_balance": "9963800",
	    "commit_fee": "36200",
	    "commit_weight": "552",
	    "fee_per_kw": "50000",
	    "unsettled_balance": "0",
	    "total_satoshis_sent": "0",
	    "total_satoshis_received": "0",
	    "num_updates": "0",
	    "pending_htlcs": []
	}
    ]
}
$ lncli --rpcserver=localhost:10001 --no-macaroons addinvoice --value=5000 --ticker=LTC
{
    "r_hash": "ff8492a7c4a58a5ead0ec323ba6602f3fa20cd18967796c8326e0076c8a4d880",
    "pay_req": "lntl50u1pdywka8pp5l7zf9f7y5k99atgwcv3m5esz70azpngcjemedjpjdcq8dj9ymzqqdqqcqzysqakr7lhcnd5kqvvz4j838u2n7f59863aps39ul0zwqwgq9wglwm4r7jpexu4283xfh84lycaxupcv82g75pqmvlsf5cgfysdjfklzysqtqzfae"
}
```

Exchange B pays `5000 Litoshi` using encoded payment request
```shell
$ lncli --rpcserver=localhost:10002 --no-macaroons getinfo
{
    "identity_pubkey": "0248a05db7c3996df2699fca9a9a1f843c723b50a6178e805416150b199b5c44bc",
    "alias": "",
    "num_pending_channels": 0,
    "num_active_channels": 2,
    "num_peers": 1,
    "block_height": 1256780,
    "block_hash": "00000000e1b4c3539106670d90e5c6cc02416cff67c58cf65a31aa69024aaa9d",
    "synced_to_chain": true,
    "testnet": true,
    "chains": [
	"litecoin",
	"bitcoin"
    ]
}
$ lncli --rpcserver=localhost:10002 --no-macaroons walletbalance --ticker=BTC
{
    "balance": "130000000"
}
$ lncli --rpcserver=localhost:10002 --no-macaroons walletbalance --ticker=LTC
{
    "balance": "989964850"
}
$ lncli --rpcserver=localhost:10002 --no-macaroons listpeers
{
    "peers": [
	{
	    "pub_key": "026a2f91860f43b03aff44246652a464e68a678251d6d6e0f24a8c4398b8333aa7",
	    "peer_id": 1,
	    "address": "127.0.0.1:10011",
	    "bytes_sent": "291719",
	    "bytes_recv": "290595",
	    "sat_sent": "0",
	    "sat_recv": "1000",
	    "inbound": true,
	    "ping_time": "267"
	}
    ]
}
$ lncli --rpcserver=localhost:10002 --no-macaroons listchannels
{
    "channels": [
	{
	    "active": true,
	    "remote_pubkey": "026a2f91860f43b03aff44246652a464e68a678251d6d6e0f24a8c4398b8333aa7",
	    "channel_point": "1051ea63b1928714c8e319eeab0abb2fb639ea7c007315f26383132c500fe077:0",
	    "chan_id": "1381475887161802752",
	    "capacity": "16000000",
	    "local_balance": "1000",
	    "remote_balance": "15990312",
	    "commit_fee": "8688",
	    "commit_weight": "724",
	    "fee_per_kw": "12000",
	    "unsettled_balance": "0",
	    "total_satoshis_sent": "0",
	    "total_satoshis_received": "1000",
	    "num_updates": "30",
	    "pending_htlcs": []
	},
	{
	    "active": true,
	    "remote_pubkey": "026a2f91860f43b03aff44246652a464e68a678251d6d6e0f24a8c4398b8333aa7",
	    "channel_point": "70b9250e7ebf4a069823d884d1fb7d23fe4a02e27f96a6905a6857724bf3f1f4:0",
	    "chan_id": "333714973170008064",
	    "capacity": "10000000",
	    "local_balance": "9963800",
	    "remote_balance": "0",
	    "commit_fee": "36200",
	    "commit_weight": "600",
	    "fee_per_kw": "50000",
	    "unsettled_balance": "0",
	    "total_satoshis_sent": "0",
	    "total_satoshis_received": "0",
	    "num_updates": "0",
	    "pending_htlcs": []
	}
    ]
}
$ lncli --rpcserver=localhost:10002 --no-macaroons sendpayment --pay_req=lntl50u1pdywka8pp5l7zf9f7y5k99atgwcv3m5esz70azpngcjemedjpjdcq8dj9ymzqqdqqcqzysqakr7lhcnd5kqvvz4j838u2n7f59863aps39ul0zwqwgq9wglwm4r7jpexu4283xfh84lycaxupcv82g75pqmvlsf5cgfysdjfklzysqtqzfae
[lncli] rpc error: code = Unknown desc = unknown network
```
