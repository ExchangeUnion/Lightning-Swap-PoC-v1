[ [index](/README.md), [previous](/LIGHTNING-03-channels.md), [next](/LIGHTNING-05-swap.md) ]
# Lightning Payment

## Wallet balances

Let's have another look at wallet balances. Going forward we are using only lightning payments so these balances will not change until we close the channels.

## Exchange A's Balances & Channel Status Pre Payment
```shell
$ lncli --rpcserver=localhost:10001 --no-macaroons walletbalance --ticker=BTC
{
        "balance": "16491564"
}
$ lncli --rpcserver=localhost:10001 --no-macaroons walletbalance --ticker=LTC
{
        "balance": "989964850"
}
$ lncli --rpcserver=localhost:10001 --no-macaroons listchannels
{
        "channels": [
                {
                        "active": true,
                        "remote_pubkey": "03af3a013b3e546ebf4c39cd5314129d740f928e26365f7de382983976608106bf",
                        "channel_point": "2610b4464f853b02c6ded664f8d000e1f85affecea68f43337e57cfbe9f9e407:0",
                        "chan_id": "1453549973873688576",
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
                        "remote_pubkey": "03af3a013b3e546ebf4c39cd5314129d740f928e26365f7de382983976608106bf",
                        "channel_point": "895799da56c1f950668360a5b0c9c668a61386001906b03b8b8235e05dc22cf8:0",
                        "chan_id": "659368327084310528",
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

### Exchange B's Balances & Channel Status Pre Payment
```shell
$ lncli --rpcserver=localhost:10002 --no-macaroons walletbalance --ticker=BTC
{
        "balance": "0"
}
$ lncli --rpcserver=localhost:10002 --no-macaroons walletbalance --ticker=LTC
{
        "balance": "0"
}
$ lncli --rpcserver=localhost:10002 --no-macaroons listchannels
{
        "channels": [
                {
                        "active": true,
                        "remote_pubkey": "025f2c07cf98f00ffd8c4c3bbf822e1bc7976cf2f5cb2b88a196d54d6a87856d7a",
                        "channel_point": "2610b4464f853b02c6ded664f8d000e1f85affecea68f43337e57cfbe9f9e407:0",
                        "chan_id": "1453549973873688576",
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
                        "remote_pubkey": "025f2c07cf98f00ffd8c4c3bbf822e1bc7976cf2f5cb2b88a196d54d6a87856d7a",
                        "channel_point": "895799da56c1f950668360a5b0c9c668a61386001906b03b8b8235e05dc22cf8:0",
                        "chan_id": "659368327084310528",
                        "capacity": "10000000",
                        "local_balance": "5000000",
                        "remote_balance": "4963800",
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

## Bitcoin Payment

### Exchange B invoice
Exchange B creates invoice for `100000 Satoshi`
```shell
$ lncli --rpcserver=localhost:10002 --no-macaroons addinvoice --value=100000 --ticker=BTC
{
        "r_hash": "595577b4fdc759a0b26415e6829efd2e020e0fec6d9913e78d23af119499e842",
        "pay_req": "lntb1m1pds7ylnpp5t92h0d8acav6pvnyzhng98ha9cpqurlvdkv38eudywh3r9yeappqdqqcqzys7pjfwckh9mkq6t5jaly26yxk33ljf635skd2r32uek8sxgw4lryh05s427zqfhzxpmdqmve69dyvgxkpe00fg0ucv38frtnrv5tzs7gqex39u3"
}
```

### Exchange A pays the invoice
Exchange A pays `100000 Satoshi` using encoded payment request
```shell
$ btc_pay_req=`lncli --rpcserver=localhost:10002 --no-macaroons  listinvoices|grep -A 4 '"value": "100000"'|grep payment_request|cut -d '"' -f 4|tail -n 1`

$ echo using payment_request $btc_pay_req
using payment_request lntb1m1pd32qp7pp5h6qnausgfkduz94j2tjp9xa8llpvn8vrre07lzgtrp3dewqfgugsdqqcqzysdre5fzmufsah5z9wnpp5rkzqxykwphcf58z8sa6gc69jkk6fj0ls3mt6rt3rhsgasdq0mkpccvn0qz85hh0gws05n9qlz3l340732sqp5m7uh2

$ lncli --rpcserver=localhost:10001 --no-macaroons sendpayment --pay_req=$btc_pay_req
{
        "payment_error": "",
        "payment_preimage": "64cbd69d8df0553b835697db6cc915ac8d351df59b57321eda0efd584df6b88f",
        "payment_route": {
                "total_time_lock": 1319180,
                "total_amt": 100000,
                "hops": [
                        {
                                "chan_id": 1450271230199529472,
                                "chan_capacity": 16000000,
                                "amt_to_forward": 100000,
                                "expiry": 1319180
                        }
                ]
        }
}
```


### Exchange A's Balances & Channel Status Post Payment
```shell
$ lncli --rpcserver=localhost:10001 --no-macaroons walletbalance --ticker=BTC
{
        "balance": "16491564"
}
$ lncli --rpcserver=localhost:10001 --no-macaroons walletbalance --ticker=LTC
{
        "balance": "989964850"
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
                        "local_balance": "15891312",
                        "remote_balance": "100000",
                        "commit_fee": "8688",
                        "commit_weight": "724",
                        "fee_per_kw": "12000",
                        "unsettled_balance": "0",
                        "total_satoshis_sent": "100000",
                        "total_satoshis_received": "0",
                        "num_updates": "2",
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



### Exchange B's Balances & Channel Status Post Payment
```shell
$ lncli --rpcserver=localhost:10002 --no-macaroons walletbalance --ticker=BTC
{
        "balance": "0"
}
$ lncli --rpcserver=localhost:10002 --no-macaroons walletbalance --ticker=LTC
{
        "balance": "0"
}
$ lncli --rpcserver=localhost:10002 --no-macaroons listchannels
{
        "channels": [
                {
                        "active": true,
                        "remote_pubkey": "026374581ff7974975ffce20e65a04876ba33405502d1a13dc73c9a702b61aef31",
                        "channel_point": "1f40907fc1968319cbb57955e06c7b11d4f3b9d413c633c1ca26288b9d2e033b:0",
                        "chan_id": "1450271230199529472",
                        "capacity": "16000000",
                        "local_balance": "100000",
                        "remote_balance": "15891312",
                        "commit_fee": "8688",
                        "commit_weight": "724",
                        "fee_per_kw": "12000",
                        "unsettled_balance": "0",
                        "total_satoshis_sent": "0",
                        "total_satoshis_received": "100000",
                        "num_updates": "2",
                        "pending_htlcs": []
                },
                {
                        "active": true,
                        "remote_pubkey": "026374581ff7974975ffce20e65a04876ba33405502d1a13dc73c9a702b61aef31",
                        "channel_point": "3c5b1d738e251819f0eaf263e73eb268e73a2d231e5da00cdfada76b3c66e8f7:0",
                        "chan_id": "649885039294873600",
                        "capacity": "10000000",
                        "local_balance": "5000000",
                        "remote_balance": "4963800",
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


## Litecoin Payment
Exchange A creates invoice for `5000 Litoshi`
```shell
$ lncli --rpcserver=localhost:10001 --no-macaroons addinvoice --value=5000 --ticker=LTC
{
        "r_hash": "b0ec9ed78d4976082adde610a7afb2bb3900646a57226269d77fa1b4444d6cab",
        "pay_req": "lntl50u1pds79v0pp5krkfa4udf9mqs2kaucg20tajhvusqer22u3xy6wh07smg3zddj4sdqqcqzjqrx292nmh94e6f0fukvs0v00hn6mflrak0za0xcvvjt3jzu8gt3ty6t4xuvvw7gv8vz72kdpnpdvashj3wkrgfmh6knw0u78m5ds6k9spwxpzdz"
}
```

Exchange B pays `5000 Litoshi` using encoded payment request
```shell

$ ltc_pay_req=`lncli --rpcserver=localhost:10001 --no-macaroons  listinvoices|grep -A 4 '"value": "5000"'|grep payment_request|cut -d '"' -f 4|tail -n 1`

$ echo using payment_request $ltc_pay_req
using payment_request lntl50u1pds79v0pp5krkfa4udf9mqs2kaucg20tajhvusqer22u3xy6wh07smg3zddj4sdqqcqzjqrx292nmh94e6f0fukvs0v00hn6mflrak0za0xcvvjt3jzu8gt3ty6t4xuvvw7gv8vz72kdpnpdvashj3wkrgfmh6knw0u78m5ds6k9spwxpzdz

$ lncli --rpcserver=localhost:10002 --no-macaroons sendpayment --pay_req=$ltc_pay_req
{
        "payment_error": "",
        "payment_preimage": "4e354ce403880af008c601b1dbf3d18a0555e7d72909ef99e7134647f57119f3",
        "payment_route": {
                "total_time_lock": 1319617,
                "total_amt": 5000,
                "hops": [
                        {
                                "chan_id": 1450271230199529472,
                                "chan_capacity": 16000000,
                                "amt_to_forward": 5000,
                                "expiry": 1319617
                        }
                ]
        }
}
```

### Exchange A's Channel Status Post Payment
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
[ [index](/README.md), [previous](/LIGHTNING-03-channels.md), [next](/LIGHTNING-05-swap.md) ]
