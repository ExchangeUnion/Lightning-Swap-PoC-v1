# Lightning Payment Channels


### Exchange A opens 0.16 BTC payment channel to Exchange B
Check open payment channels for Exchange A
```shell
$ lncli --rpcserver=localhost:10001 --no-macaroons listchannels
{
    "channels": []
}
```

Exchange A tries to open `0.9 BTC` channel to Exchange B, getting limit error imposed by the [spec](https://github.com/lightningnetwork/lightning-rfc/blob/master/02-peer-protocol.md#requirements)
```shell
$ lncli --rpcserver=localhost:10001 --no-macaroons openchannel --node_key=0248a05db7c3996df2699fca9a9a1f843c723b50a6178e805416150b199b5c44bc --local_amt=90000000 --ticker=BTC
[lncli] rpc error: code = Unknown desc = funding amount is too large, the max channel size is: 0.16777216 BTC
```

Exchange A opens `0.16 BTC` channel to Exchange B with the following [BTC funding transaction](https://www.blocktrail.com/tBTC/tx/1051ea63b1928714c8e319eeab0abb2fb639ea7c007315f26383132c500fe077)
```shell
$ lncli --rpcserver=localhost:10001 --no-macaroons openchannel --node_key=0248a05db7c3996df2699fca9a9a1f843c723b50a6178e805416150b199b5c44bc --local_amt=16000000 --ticker=BTC
{
    "funding_txid": "1051ea63b1928714c8e319eeab0abb2fb639ea7c007315f26383132c500fe077"
}

```

Exchange A lists the `Bitcoin` payment channel as follows
```shell
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
	    "num_updates": "0",
	    "pending_htlcs": []
	}
    ]
}
```

Exchange B lists the `Bitcoin` payment channel as follows
```
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
	    "num_updates": "0",
	    "pending_htlcs": []
	}
    ]
}
```

Exchange A's `Bitcoin` balance decreased due to the committed amount in payment channel
```
$ lncli --rpcserver=localhost:10001 --no-macaroons walletbalance --ticker=BTC
{
    "balance": "83991564"
}
$ lncli --rpcserver=localhost:10001 --no-macaroons walletbalance --ticker=LTC
{
    "balance": "10000000"
}
```


### Checking Swap Routes
Exchange A has no swap route to Exchange B yet
```shell
$ lncli --rpcserver=localhost:10001 --no-macaroons queryswaproutes --dest=0248a05db7c3996df2699fca9a9a1f843c723b50a6178e805416150b199b5c44bc --in_amt=1000 --in_ticker=BTC --out_ticker=LTC
[lncli] rpc error: code = Unknown desc = unable to find a path to destination
$ lncli --rpcserver=localhost:10001 --no-macaroons queryswaproutes --dest=0248a05db7c3996df2699fca9a9a1f843c723b50a6178e805416150b199b5c44bc --in_amt=1000 --in_ticker=LTC --out_ticker=BTC
[lncli] rpc error: code = Unknown desc = unable to find a path to destination

```

Exchange B has no swap route to Exchange A yet
```shell
$ lncli --rpcserver=localhost:10002 --no-macaroons queryswaproutes --dest=026a2f91860f43b03aff44246652a464e68a678251d6d6e0f24a8c4398b8333aa7 --in_amt=1000 --in_ticker=BTC --out_ticker=LTC
[lncli] rpc error: code = Unknown desc = unable to find a path to destination
$ lncli --rpcserver=localhost:10002 --no-macaroons queryswaproutes --dest=026a2f91860f43b03aff44246652a464e68a678251d6d6e0f24a8c4398b8333aa7 --in_amt=1000 --in_ticker=LTC --out_ticker=BTC
[lncli] rpc error: code = Unknown desc = unable to find a path to destination
```



### Exchange B opens 0.1 LTC payment channel to Exchange A
Check open payment channels for Exchange B (only BTC channel to Exchange A exists)
```shell
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
	    "num_updates": "0",
	    "pending_htlcs": []
	}
    ]
}
```

Exchange B opens `0.1 LTC` channel to Exchange A with the following [LTC funding transaction](https://chain.so/tx/LTCTEST/70b9250e7ebf4a069823d884d1fb7d23fe4a02e27f96a6905a6857724bf3f1f4)
```shell
$ lncli --rpcserver=localhost:10002 --no-macaroons openchannel --node_key=026a2f91860f43b03aff44246652a464e68a678251d6d6e0f24a8c4398b8333aa7 --local_amt=10000000 --ticker=LTC
{
    "funding_txid": "70b9250e7ebf4a069823d884d1fb7d23fe4a02e27f96a6905a6857724bf3f1f4"
}
```

Exchange B lists the `Litecoin` payment channel as follows
```shell
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
	    "num_updates": "0",
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

Exchange A lists the `Litecoin` payment channel as follows
```shell
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
	    "num_updates": "0",
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

NOTE: Exchange B's `Litecoin` balance is locked until funding transaction is confirmed
```shell
$ lncli --rpcserver=localhost:10002 --no-macaroons walletbalance --ticker=BTC
{
    "balance": "130000000"
}
$ lncli --rpcserver=localhost:10002 --no-macaroons walletbalance --ticker=LTC
{
    "balance": "0"
}
```

Exchange B's `Litecoin` balance after funding transaction is confirmed
```shell
$ lncli --rpcserver=localhost:10002 --no-macaroons walletbalance --ticker=BTC
{
    "balance": "130000000"
}
$ lncli --rpcserver=localhost:10002 --no-macaroons walletbalance --ticker=LTC
{
    "balance": "989964850"
}
```


### Checking Swap Routes
Exchange A has the following swap routes to Exchange B
```shell
$ lncli --rpcserver=localhost:10001 --no-macaroons queryswaproutes --dest=0248a05db7c3996df2699fca9a9a1f843c723b50a6178e805416150b199b5c44bc --in_amt=1000 --in_ticker=BTC --out_ticker=LTC
{
    "routes": [
	{
	    "total_time_lock": 304124,
	    "total_fees": "0",
	    "total_amt": "100000",
	    "hops": [
		{
		    "chan_id": "333714973170008064",
		    "chan_capacity": "10000000",
		    "amt_to_forward": "100000",
		    "fee": "0",
		    "expiry": 303548
		},
		{
		    "chan_id": "1381475887161802752",
		    "chan_capacity": "16000000",
		    "amt_to_forward": "1000",
		    "fee": "0",
		    "expiry": 1256476
		}
	    ]
	}
    ]
}
$ lncli --rpcserver=localhost:10001 --no-macaroons queryswaproutes --dest=0248a05db7c3996df2699fca9a9a1f843c723b50a6178e805416150b199b5c44bc --in_amt=1000 --in_ticker=LTC --out_ticker=BTC
{
    "routes": [
	{
	    "total_time_lock": 1256615,
	    "total_fees": "0",
	    "total_amt": "10",
	    "hops": [
		{
		    "chan_id": "1381475887161802752",
		    "chan_capacity": "16000000",
		    "amt_to_forward": "10",
		    "fee": "0",
		    "expiry": 1256471
		},
		{
		    "chan_id": "333714973170008064",
		    "chan_capacity": "10000000",
		    "amt_to_forward": "1000",
		    "fee": "0",
		    "expiry": 303521
		}
	    ]
	}
    ]
}
```

Exchange B has the following swap routes to Exchange A
```shell
$ lncli --rpcserver=localhost:10002 --no-macaroons queryswaproutes --dest=026a2f91860f43b03aff44246652a464e68a678251d6d6e0f24a8c4398b8333aa7 --in_amt=1000 --in_ticker=BTC --out_ticker=LTC
{
    "routes": [
	{
	    "total_time_lock": 304124,
	    "total_fees": "0",
	    "total_amt": "100000",
	    "hops": [
		{
		    "chan_id": "333714973170008064",
		    "chan_capacity": "10000000",
		    "amt_to_forward": "100000",
		    "fee": "0",
		    "expiry": 303548
		},
		{
		    "chan_id": "1381475887161802752",
		    "chan_capacity": "16000000",
		    "amt_to_forward": "1000",
		    "fee": "0",
		    "expiry": 1256478
		}
	    ]
	}
    ]
}
$ lncli --rpcserver=localhost:10002 --no-macaroons queryswaproutes --dest=026a2f91860f43b03aff44246652a464e68a678251d6d6e0f24a8c4398b8333aa7 --in_amt=1000 --in_ticker=LTC --out_ticker=BTC
{
    "routes": [
	{
	    "total_time_lock": 1256615,
	    "total_fees": "0",
	    "total_amt": "10",
	    "hops": [
		{
		    "chan_id": "1381475887161802752",
		    "chan_capacity": "16000000",
		    "amt_to_forward": "10",
		    "fee": "0",
		    "expiry": 1256471
		},
		{
		    "chan_id": "333714973170008064",
		    "chan_capacity": "10000000",
		    "amt_to_forward": "1000",
		    "fee": "0",
		    "expiry": 303521
		}
	    ]
	}
    ]
}
```
