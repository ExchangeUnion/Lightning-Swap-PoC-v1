[ [index](/README.md), [previous](/LIGHTNING-04-payment.md) ]

# Lightning Cross-Chain Swap

## LND processes restart
```diffl
-Before we execute the atomic swap, we need to restart the lnd processes of exchange A and B again. 
-You can use control+c at the terminal to do that. 
```

## Finding a Swap Route
Exchange A queries a swap route to exchange `1000 Satoshi` for `0.001 LTC` with Exchange B (1:100 fixed for now)
Exchange A also creates an invoice of `100000 Litoshi = 0.001 LTC` to be paid by itself to execute the swap
```shell
$ lncli --rpcserver=localhost:10001 --no-macaroons getinfo
{
        "identity_pubkey": "026374581ff7974975ffce20e65a04876ba33405502d1a13dc73c9a702b61aef31",
        "alias": "",
        "num_pending_channels": 0,
        "num_active_channels": 2,
        "num_peers": 1,
        "block_height": 1319589,
        "block_hash": "0000000000000d57858c03e9b4ae79f1e9e859342f3207b013bb705ba1178d30",
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
                        "pub_key": "0237cdf6b03cf17df8676af35b43da3ee0613b888bc5cd26a41064118f1241cc2f",
                        "peer_id": 1,
                        "address": "127.0.0.1:56624",
                        "bytes_sent": "20079",
                        "bytes_recv": "19913",
                        "sat_sent": "100000",
                        "sat_recv": "5000",
                        "inbound": false,
                        "ping_time": "312"
                }
        ]
}
$ lncli --rpcserver=localhost:10001 --no-macaroons listchannels
{
        "channels": [
                {
                        "active": true,
                        "remote_pubkey": "0237cdf6b03cf17df8676af35b43da3ee0613b888bc5cd26a41064118f1241cc2f",
                        "channel_point": "1f40907fc1968319cbb57955e06c7b11d4f3b9d413c633c1ca26288b9d2e033b:0",
                        "chan_id": "1450271230199529472",
                        "capacity": "16000000",
                        "local_balance": "15896312",
                        "remote_balance": "95000",
                        "commit_fee": "8688",
                        "commit_weight": "724",
                        "fee_per_kw": "12000",
                        "unsettled_balance": "0",
                        "total_satoshis_sent": "100000",
                        "total_satoshis_received": "5000",
                        "num_updates": "4",
                        "pending_htlcs": []
                },
                {
                        "active": true,
                        "remote_pubkey": "0237cdf6b03cf17df8676af35b43da3ee0613b888bc5cd26a41064118f1241cc2f",
                        "channel_point": "3c5b1d738e251819f0eaf263e73eb268e73a2d231e5da00cdfada76b3c66e8f7:0",
                        "chan_id": "649885039294873600",
                        "capacity": "10000000",
                        "local_balance": "4963800",
                        "remote_balance": "5000000",
                        "commit_fee": "36200",
                        "commit_weight": "724",
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

Let's see if there is a route to support the swap

```shell
$ lncli --rpcserver=localhost:10001 --no-macaroons queryswaproutes --dest=$X_B_ID_PUBKEY --in_amt=100000 --in_ticker=LTC --out_ticker=BTC
{
        "routes": [
                {
                        "total_time_lock": 1319737,
                        "total_fees": "0",
                        "total_amt": "1000",
                        "hops": [
                                {
                                        "chan_id": "1450271230199529472",
                                        "chan_capacity": "16000000",
                                        "amt_to_forward": "1000",
                                        "fee": "0",
                                        "expiry": 1319593
                                },
                                {
                                        "chan_id": "649885039294873600",
                                        "chan_capacity": "10000000",
                                        "amt_to_forward": "100000",
                                        "fee": "0",
                                        "expiry": 591177
                                }
                        ]
                }
        ]
}
```
Create an invoice to accept the `100000 Litoshi`
```shell
$ lncli --rpcserver=localhost:10001 --no-macaroons addinvoice --value=100000 --ticker=LTC
{
        "r_hash": "8f2b4ae784731ca1fe4f6165a6db0703b2ae916fd53a0e82645ab71fcb58d617",
        "pay_req": "lntl1m1pdslr0upp53u454euywvw2rlj0v9j6dkc8qwe2ayt065aqaqnyt2m3lj6c6ctsdqqcqzjqstwjw3r7yv5fsjtvnxgvsxpm56nref83arvn93qkjlhc3qug3jws7rktatz4hq27fumqf92ay6qs2v40khyhzg2xwwww4nq7na0vw3cq5em4t4"
}
```

Exchange A executes the swap - sending BTC accepting LTC
```diff
-Make sure you use the `r_hash` provided by the above create invoice
```
```shell
$ lncli --rpcserver=localhost:10001 --no-macaroons queryswaproutes --dest=$X_B_ID_PUBKEY --in_amt=100000 --in_ticker=LTC --out_ticker=BTC |lncli --rpcserver=localhost:10001 --no-macaroons sendtoroute --payment_hash 8f2b4ae784731ca1fe4f6165a6db0703b2ae916fd53a0e82645ab71fcb58d617
{
        "payment_error": "",
        "payment_preimage": "1c307f5f887ab7f95a66881fbb214f015397642f84d141d150ad7dda4af212f8",
        "payment_route": {
                "total_time_lock": 1319741,
                "total_amt": 1000,
                "hops": [
                        {
                                "chan_id": 1450271230199529472,
                                "chan_capacity": 16000000,
                                "amt_to_forward": 1000,
                                "expiry": 1319597
                        },
                        {
                                "chan_id": 649885039294873600,
                                "chan_capacity": 10000000,
                                "amt_to_forward": 100000,
                                "expiry": 591178
                        }
                ]
        }
}
```
Let's see the impact in the channels (check `local_balance` and `remote_balance`)
```shell
$  lncli --rpcserver=localhost:10001 --no-macaroons listchannels
{
        "channels": [
                {
                        "active": true,
                        "remote_pubkey": "0237cdf6b03cf17df8676af35b43da3ee0613b888bc5cd26a41064118f1241cc2f",
                        "channel_point": "1f40907fc1968319cbb57955e06c7b11d4f3b9d413c633c1ca26288b9d2e033b:0",
                        "chan_id": "1450271230199529472",
                        "capacity": "16000000",
                        "local_balance": "15895312",
                        "remote_balance": "96000",
                        "commit_fee": "8688",
                        "commit_weight": "724",
                        "fee_per_kw": "12000",
                        "unsettled_balance": "0",
                        "total_satoshis_sent": "101000",
                        "total_satoshis_received": "5000",
                        "num_updates": "8",
                        "pending_htlcs": []
                },
                {
                        "active": true,
                        "remote_pubkey": "0237cdf6b03cf17df8676af35b43da3ee0613b888bc5cd26a41064118f1241cc2f",
                        "channel_point": "3c5b1d738e251819f0eaf263e73eb268e73a2d231e5da00cdfada76b3c66e8f7:0",
                        "chan_id": "649885039294873600",
                        "capacity": "10000000",
                        "local_balance": "5063800",
                        "remote_balance": "4900000",
                        "commit_fee": "36200",
                        "commit_weight": "724",
                        "fee_per_kw": "50000",
                        "unsettled_balance": "0",
                        "total_satoshis_sent": "0",
                        "total_satoshis_received": "100000",
                        "num_updates": "2",
                        "pending_htlcs": []
                }
        ]
}
```
Create an invoice to accept `300 Satoshi`
```shell
$ lncli --rpcserver=localhost:10001 --no-macaroons addinvoice --value=300 --ticker=BTC
{
        "r_hash": "65dd55deed719fed4dc2bebac42a6bc113de70b9826d3276c45cf0b78c3716fa",
        "pay_req": "lntb3u1pdslrlzpp5vhw4thhdwx076nwzh6avg2ntcyfauu9esfknyakytnct0rphzmaqdqqcqzysxuhrquw2tw3ttwcdyutzd292rh4dkfc494lemq07fgkjnfcdlwssqmg6ema2vlyzcvn8dxl50573jsurp5z6u9vj5p23gezs77qx5xspx2w082"
}
```
Is there a route?
```shell
$  lncli --rpcserver=localhost:10001 --no-macaroons queryswaproutes --dest=$X_B_ID_PUBKEY --in_amt=300 --in_ticker=BTC --out_ticker=LTC
{
        "routes": [
                {
                        "total_time_lock": 591782,
                        "total_fees": "0",
                        "total_amt": "30000",
                        "hops": [
                                {
                                        "chan_id": "649885039294873600",
                                        "chan_capacity": "10000000",
                                        "amt_to_forward": "30000",
                                        "fee": "0",
                                        "expiry": 591206
                                },
                                {
                                        "chan_id": "1450271230199529472",
                                        "chan_capacity": "16000000",
                                        "amt_to_forward": "300",
                                        "fee": "0",
                                        "expiry": 1319611
                                }
                        ]
                }
        ]
}
```
Lets try to swap LTC for BTC
```diff
-Make sure you use the `r_hash` provided by the above create invoice
```

```shell
$ lncli --rpcserver=localhost:10001 --no-macaroons queryswaproutes --dest=$X_B_ID_PUBKEY --in_amt=300 --in_ticker=BTC --out_ticker=LTC|lncli --rpcserver=localhost:10001 --no-macaroons sendtoroute --payment_hash 65dd55deed719fed4dc2bebac42a6bc113de70b9826d3276c45cf0b78c3716fa
{
        "payment_error": "",
        "payment_preimage": "3b6610d7ead10d074b1f7da9a378d493fea6630e019837a66addf84f1be3075f",
        "payment_route": {
                "total_time_lock": 591782,
                "total_amt": 30000,
                "hops": [
                        {
                                "chan_id": 649885039294873600,
                                "chan_capacity": 10000000,
                                "amt_to_forward": 30000,
                                "expiry": 591206
                        },
                        {
                                "chan_id": 1450271230199529472,
                                "chan_capacity": 16000000,
                                "amt_to_forward": 300,
                                "expiry": 1319611
                        }
                ]
        }
}
```

Looks good. Final look at the channels
```shell
$ lncli --rpcserver=localhost:10001 --no-macaroons listchannels
{
        "channels": [
                {
                        "active": true,
                        "remote_pubkey": "0237cdf6b03cf17df8676af35b43da3ee0613b888bc5cd26a41064118f1241cc2f",
                        "channel_point": "1f40907fc1968319cbb57955e06c7b11d4f3b9d413c633c1ca26288b9d2e033b:0",
                        "chan_id": "1450271230199529472",
                        "capacity": "16000000",
                        "local_balance": "15895612",
                        "remote_balance": "95700",
                        "commit_fee": "8688",
                        "commit_weight": "724",
                        "fee_per_kw": "12000",
                        "unsettled_balance": "0",
                        "total_satoshis_sent": "101000",
                        "total_satoshis_received": "5300",
                        "num_updates": "10",
                        "pending_htlcs": []
                },
                {
                        "active": true,
                        "remote_pubkey": "0237cdf6b03cf17df8676af35b43da3ee0613b888bc5cd26a41064118f1241cc2f",
                        "channel_point": "3c5b1d738e251819f0eaf263e73eb268e73a2d231e5da00cdfada76b3c66e8f7:0",
                        "chan_id": "649885039294873600",
                        "capacity": "10000000",
                        "local_balance": "5033800",
                        "remote_balance": "4930000",
                        "commit_fee": "36200",
                        "commit_weight": "724",
                        "fee_per_kw": "50000",
                        "unsettled_balance": "0",
                        "total_satoshis_sent": "30000",
                        "total_satoshis_received": "100000",
                        "num_updates": "4",
                        "pending_htlcs": []
                }
        ]
}
```

Done!!!!

[ [index](/README.md), [previous](/LIGHTNING-04-payment.md) ]
