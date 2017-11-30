# Exchange_Union_Chain

1.	Executive Summary
Exchange Union’s (XU) underlying technology is based on a sidechain pegged to the bitcoin blockchain. It is implemented as a 2-way peg, meaning xBTC on the Exchange Union sidechain represent real bitcoin from the parent bitcoin blockchain. The decision fell for bitcoin, because it trades against virtually any other digital asset on almost all exchanges and thus currently represents the best intermediary. Also, bitcoin’s blockchain is the largest and best secured nowadays, which gives our sidechain additional security. For every cross-exchange trade in the union, xBTC are transferred between exchanges. XUC, the native token of Exchange Union is used to fuel transactions and incentivizing functionaries.
2.	Product Description
2.1	Characteristics
●	Trustless; transaction = settlement; each trade transfers real bitcoin between the exchanges
●	No IOUs; no vetting process needed - any exchange can join
●	Decentralized; each exchange runs its own XU node
●	Real-time; transaction settle within seconds
●	Confidential; transaction data visible for involved parties only
●	User Experience; Users have a global ID on all exchanges and instant login+KYC, via strategic partnership with 3rd party decentralized blockchain ID provider.
●	Automated; transfers are automated; for the user, XU behaves like one account and one UI
●	Federation; Blocks on the the XU sidechain are signed by a federation, not miners, which allows for signing and settlement within seconds. Its functionaries are chosen and evaluated by algorithms. Honest behavior is incentivized with XUC. Reference: Strong federations
2.2 	Sidechain Visualization
 
3.	Use Cases
●	Cross-exchange trading (access to all trading pairs, best price discovery)
●	Access to larger user base & increased liquidity
●	Frictionless end-user experience (instant account setup & KYC)

4. Transaction Flow & XUC Usage
 
●	To avoid spamming and DDoS attacks, every transaction must destroy (‘burn’) a small amount of XUC. 
●	The required amount of XUC per transaction is determined by a client function. It sometimes increases due to higher load. There is a fixed minimum XUC cost required by the network for a standard transaction. Adjusting this value requires consensus among the functionaries. This gives XUC a deflationary nature. Decoupling the denomination of fees (XUC) from the underlying asset (BTC) also helps to better handle volatile fees.
●	The XU node’s software allows an automated locking and unlocking of BTC/xBTC based on thresholds set by the exchange which runs the node. This allows exchanges to choose the amount of BTC to hold on the sidechain and automatically adjust if trading volume goes up or down.
●	XUC is also used as incentive for functionaries (e.g. block signers) to stay honest.
●	In short, XUC functions as fee for transactions and as incentive for functionaries & exchanges.
5. What is different from Ripple/Stellar?
●	Exchange Union’s transactions are stored in blocks which form a blockchain, not balances maintained in a Distributed Ledger like in Ripple/Stellar
●	Exchange Union is trustless; Ripple/Stellar requires building up a trust network, which exposes involved parties to counterparty risk. This would cause loss of funds when an exchange goes bankrupt or hacked and thus is not acceptable.
●	Consequently in Exchange Union there is always real value is transferred, bitcoin, no IOUs
●	Reduced setup costs, e.g. no requirements to build up a trust network or maintain list of untrusted parties
6. What is different from Blockstream’s Liquid?
●	Reduced setup costs (e.g. no hardware required)
●	Automated within each trade (no handling of X, Q or H liquid addresses, no manual triggering of transactions)
●	More end-user oriented: No multiple account setups & multiple KYC needed
7. What is different from Lightning?
●	Trades are securely, immutably and confidentially recorded on the union chain. This is primarily needed for fee sharing transparency between the involved parties and likely will be legally required in future
●	Lightning is not designed to permanently record transactions

