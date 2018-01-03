# Lightning Cross-Chain Swap

### Finding a Swap Route
Exchange A checks queries a swap route to exchange `1000 Satoshi` for `0.001 LTC` with Exchange B (1:100 is fixed for now)
Exchange A also creates an invoice of `100000 Litoshi = 0.001 LTC` to be paid by itself to execute the swap
```shell


$ lncli --rpcserver=localhost:10001 --no-macaroons getinfo
{
    "identity_pubkey": "026a2f91860f43b03aff44246652a464e68a678251d6d6e0f24a8c4398b8333aa7",
    "alias": "",
    "num_pending_channels": 0,
    "num_active_channels": 2,
    "num_peers": 1,
    "block_height": 1256782,
    "block_hash": "00000000000005d55ae90fbf226422bc684d4ddf89c57e82f85c17fedcba9181",
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
	    "peer_id": 3,
	    "address": "127.0.0.1:37780",
	    "bytes_sent": "290543",
	    "bytes_recv": "290401",
	    "sat_sent": "1000",
	    "sat_recv": "0",
	    "inbound": false,
	    "ping_time": "511"
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
$ lncli --rpcserver=localhost:10001 --no-macaroons queryswaproutes --dest=0248a05db7c3996df2699fca9a9a1f843c723b50a6178e805416150b199b5c44bc --in_amt=100000 --in_ticker=LTC --out_ticker=BTC
{
    "routes": [
	{
	    "total_time_lock": 1256928,
	    "total_fees": "0",
	    "total_amt": "1000",
	    "hops": [
		{
		    "chan_id": "1381475887161802752",
		    "chan_capacity": "16000000",
		    "amt_to_forward": "1000",
		    "fee": "0",
		    "expiry": 1256784
		},
		{
		    "chan_id": "333714973170008064",
		    "chan_capacity": "10000000",
		    "amt_to_forward": "100000",
		    "fee": "0",
		    "expiry": 315008
		}
	    ]
	}
    ]
}
$ lncli --rpcserver=localhost:10001 --no-macaroons addinvoice --value=100000 --ticker=LTC
{
    "r_hash": "3e17f9cfbc119ba7d9db994e0542271d7beecb442fbd9be6b0e4cb99718634e1",
    "pay_req": "lntl1m1pdywexwpp58ctlnnauzxd60kwmn98q2s38r4a7aj6y977ehe4sun9ejuvxxnssdqqcqzysf0293s6er2kftthxyr6s26cpjedflrvfxuwzfxcrwqmk5k5cdyx9x599jsga0pru47txnvg6uc0pcngsh50vcp3t2tguqeve5uafxrqpkkqvsd"
}
```

Exchange A executes swap but failed
```shell
$ lncli --rpcserver=localhost:10001 --no-macaroons queryswaproutes --dest=0248a05db7c3996df2699fca9a9a1f843c723b50a6178e805416150b199b5c44bc --in_amt=100000 --in_ticker=LTC --out_ticker=BTC |lncli --rpcserver=localhost:10001 --no-macaroons sendtoroute --payment_hash 3e17f9cfbc119ba7d9db994e0542271d7beecb442fbd9be6b0e4cb99718634e1
[lncli] rpc error: code = Unknown desc = FeeInsufficient(fee=1000000 mSAT, update=(lnwire.ChannelUpdate) {
 Signature: (*btcec.Signature)(0xc420192730)({
  R: (*big.Int)(0xc43090da40)(88852869513240570222289048999464488755272702035603010628486187315859392873274),
  S: (*big.Int)(0xc43090da60)(52380659601087727064220200464019313077696085382491650331947942506696022152217)
 }),
 ChainHash: (chainhash.Hash) (len=32 cap=32) 000000000933ea01ad0ee984209779baaec3ced90fa3f408719526f8d77f4943,
 ShortChannelID: (lnwire.ShortChannelID) {
  BlockHeight: (uint32) 1256445,
  TxIndex: (uint32) 12,
  TxPosition: (uint16) 0
 },
 Timestamp: (uint32) 1514625466,
 Flags: (uint16) 1,
 TimeLockDelta: (uint16) 144,
 HtlcMinimumMsat: (lnwire.MilliSatoshi) 0 mSAT,
 BaseFee: (uint32) 1000,
 FeeRate: (uint32) 1
}
```

Error log from Exchange A is as follows
```shell
2017-12-30 13:11:27.927 [INF] CRTR: Found edge with chanid=f4f1f34b7257685a90a6967fe2024afe237dfbd184d82398064abf7e0e25b970
2017-12-30 13:11:27.963 [INF] CRTR: Found edge with chanid=77e00f502c138363f21573007cea39b62fbb0aabee19e3c8148792b163ea5110
2017-12-30 13:11:27.983 [INF] RPCS: rpcroute: (*lnrpc.Route)(0xc430f4a000)(total_time_lock:1256928 total_amt:1000 hops:<chan_id:1381475887161802752 chan_capacity:16000000 amt_to_forward:1000 expiry:1256784 > hops:<chan_id:333714973170008064 chan_capacity:10000000 amt_to_forward:100000 expiry:315011 > )

2017-12-30 13:11:27.984 [INF] RPCS: route[0]: hop=0 expiry=1256784
2017-12-30 13:11:27.984 [INF] RPCS: route[0]: hop=1 expiry=315011
2017-12-30 13:11:27.984 [INF] CRTR: forwarding to chain: 000000000933ea01ad0ee984209779baaec3ced90fa3f408719526f8d77f4943
2017-12-30 13:11:27.984 [INF] CRTR: forwarding with amt: 1000000 mSAT
2017-12-30 13:11:27.984 [INF] CRTR: forwarding with cltv: 1256784
2017-12-30 13:11:27.984 [INF] CRTR: forwarding to chain: 4966625a4b2851d9fdee139e56211a0d88575f59ed816ff5e6a63deb4e3e29a0
2017-12-30 13:11:27.984 [INF] CRTR: forwarding with amt: 100000000 mSAT
2017-12-30 13:11:27.984 [INF] CRTR: forwarding with cltv: 315011
2017-12-30 13:11:27.985 [INF] CRTR: sending payment to switch along route of length=2
2017-12-30 13:11:27.986 [INF] HSWC: Setting first hop destination to: 77e00f502c138363f21573007cea39b62fbb0aabee19e3c8148792b163ea5110
2017-12-30 13:11:27.986 [INF] HSWC: packet src={0 0 0} dest=77e00f502c138363f21573007cea39b62fbb0aabee19e3c8148792b163ea5110 destNode=[2 72 160 93 183 195 153 109 242 105 159 202 154 154 31 132 60 114 59 80 166 23 142 128 84 22 21 11 25 155 92 68 188] amt=1000000 mSAT
2017-12-30 13:11:27.986 [INF] HSWC: Found short={1256445 12 0} link=77e00f502c138363f21573007cea39b62fbb0aabee19e3c8148792b163ea5110, bandwidth=15990312000 mSAT
2017-12-30 13:11:28.011 [INF] PEER: active channel found?: true
2017-12-30 13:11:28.011 [INF] PEER: retrieving service for chain 000000000933ea01ad0ee984209779baaec3ced90fa3f408719526f8d77f4943
2017-12-30 13:11:28.012 [INF] PEER: active channel found?: true
2017-12-30 13:11:28.012 [INF] PEER: retrieving service for chain 000000000933ea01ad0ee984209779baaec3ced90fa3f408719526f8d77f4943
2017-12-30 13:11:28.017 [INF] PEER: active channel found?: true
2017-12-30 13:11:28.017 [INF] PEER: retrieving service for chain 000000000933ea01ad0ee984209779baaec3ced90fa3f408719526f8d77f4943
2017-12-30 13:11:28.018 [INF] PEER: active channel found?: true
2017-12-30 13:11:28.018 [INF] PEER: retrieving service for chain 000000000933ea01ad0ee984209779baaec3ced90fa3f408719526f8d77f4943
2017-12-30 13:11:28.024 [INF] PEER: active channel found?: true
2017-12-30 13:11:28.024 [INF] PEER: retrieving service for chain 000000000933ea01ad0ee984209779baaec3ced90fa3f408719526f8d77f4943
2017-12-30 13:11:28.026 [ERR] CRTR: Attempt to send payment to route 3e17f9cfbc119ba7d9db994e0542271d7beecb442fbd9be6b0e4cb99718634e1 failed: FeeInsufficient(fee=1000000 mSAT, update=(lnwire.ChannelUpdate) {
 Signature: (*btcec.Signature)(0xc420192730)({
  R: (*big.Int)(0xc43090da40)(88852869513240570222289048999464488755272702035603010628486187315859392873274),
  S: (*big.Int)(0xc43090da60)(52380659601087727064220200464019313077696085382491650331947942506696022152217)
 }),
 ChainHash: (chainhash.Hash) (len=32 cap=32) 000000000933ea01ad0ee984209779baaec3ced90fa3f408719526f8d77f4943,
 ShortChannelID: (lnwire.ShortChannelID) {
  BlockHeight: (uint32) 1256445,
  TxIndex: (uint32) 12,
  TxPosition: (uint16) 0
 },
 Timestamp: (uint32) 1514625466,
 Flags: (uint16) 1,
 TimeLockDelta: (uint16) 144,
 HtlcMinimumMsat: (lnwire.MilliSatoshi) 0 mSAT,
 BaseFee: (uint32) 1000,
 FeeRate: (uint32) 1
}

2017-12-30 13:11:33.179 [INF] HSWC: Sent 0 satoshis received 0 satoshis in the last 10 seconds (0.2 tx/sec)
```

