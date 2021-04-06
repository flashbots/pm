# Flashbots Alpha - Searcher Onboarding

A collection of relevant information for searchers looking to use Flashbots Alpha. This document will be continuously updated as we receive more questions from you.

<br/> _Updated: 6.04.2021. Check Flashbots Discord [#release](https://discord.gg/Z26a7e2x) channel for the latest releases._ 

## Contents
1. [Summary](#summary)
2. [FAQ](#frequently-asked-questions)
    * [General](#general)
    * [Implementation](#implementation)
4. [Resources](#resources)

## Summary
### What is Flashbots Alpha?

Flashbots Alpha is a proof-of-concept implementation of a direct communication channel between miners and Ethereum users that would like to communicate their preferred transaction order within a block. This proof-of-concept is made of two components: MEV-Geth, a slightly modified fork of the Ethereum Geth client, and MEV-Relay, a transaction bundle relayer.

### How does it work?

* Searchers send Flashbots "bundles" to MEV-Relay. A bundle contains:
  * one or several transactions that can be the trader's and/or other users' pending transactions from the mempool
  * a sealed tip that is paid by the searcher to the miner via a smart contract call to `block.coinbase.transfer()`
* Moreover, bundles have these properties:
  * There can only be a single bundle per block (we are working on bundle merging to enable multiple)
  * Flashbots bundles will always be at the top slot of the block
* MEV-Relay receives bundles and sends them to all whitelisted MEV-Geth miners
* Miners receive Flashbots bundles from MEV-Relay and process them in MEV-Geth
* MEV-Geth picks the most profitable bundle out of all bundles it is sent.
* MEV-Geth then compares the block that includes this bundle with a vanilla block that does not include any bundles. If it is more profitable to include a bundle MEV-Geth will do so, but otherwise it will default back to a vanilla Geth block.
* Only when the a searcher's bundle is included in a block is the tip associated with their bundle paid.
  * If a bundle is not included it does not cost the searcher anything (i.e. no gas fees are paid for failed transactions)

### Why use Flashbots Alpha?
* It allows searchers to bypass the Ethereum mempool and avoid their strategy leaking before it is mined on-chain (e.g. being frontrun by generalized frontrunners)
* It allows searchers to save money from avoiding to pay gas fees for failed transactions.
* It allows miners to receive additional revenue in the form of the bundle tip, in exchange for including the most profitable bundle in the block they mined.
* It reduces Ethereum network congestion and lowers Ethereum network transaction fees.

### Onboard Flashbots Alpha as a Searcher

You can start using Flashbots Alpha today by submitting transaction bundles to `relay.flashbots.net`.

You'll need to:
- replace `eth_sendRawTransaction` by `eth_sendBundle`, either manually or using one of our providers (web3.py and [ethers.js](https://github.com/flashbots/ethers-provider-flashbots-bundle))
- change the gas calculation logic of your bot
- change your smart contract logic to pay a tip to the miner in the case of success by using `block.coinbase.transfer()`


We go over this in further detail in the FAQ below.

See you on-chain ! :zap:🤖

Resources to get started:
* Simple-arbitrage-searcher example bot we've open-sourced here: [simple-arbitrage-searcher]()
* Dedicated [#🤖searchers](https://discord.gg/KNFBvZzJyT) channel on Discord!
* Full [list](#resources) of resources


## Frequently Asked Questions (FAQs)
## General

### Who is behind Flashbots Alpha?

The Flashbots organization is behind Flashbots Alpha. We are a research and development organization working on solving the problems MEV causes to state-rich blockchains. You can find out more about the organization on our [pm repo](https://github.com/flashbots/pm) and in this introductory [Medium post](https://medium.com/flashbots/frontrunning-the-mev-crisis-40629a613752) that details our values and motives. 


### Why is this an Alpha?

As laid out in our [EthResearch post](https://ethresear.ch/t/flashbots-frontrunning-the-mev-crisis/8251), we look at the development of Flashbots in phases. The current infrastructure available to you is in the Alpha phase. 

| Stage                | PGA | DarkPool | Alpha | Beta | 1.0 |
| -------------------- |:---:|:--------:|:-----:|:----:|:---:|
| Pre-trade privacy    | ❌  |    ✅    |  ✅   |  ✅  | ✅  |
| Failed trade privacy | ❌  |    ✅    |  ✅   |  ✅  | ✅  |
| Efficiency           | ❌  |    ❌    |  ✅   |  ✅  | ✅  |
| Miner privacy        | ❌  |    ❌    |  ❌   |  ✅  | ✅  |
| Spam protection      | ✅  |    ❌    |  ❌   |  ❌  | ✅  |
| Finality protection  | ❌  |    ❌    |  ❌   |  ❓  | ✅  |
| Bundle merging       | ❌  |    ❌    |  ❌   |  ❓  | ❓  |

### What's MEV-Geth?

MEV-Geth is a fork of the Geth Ethereum client, modified to accept Flashbots bundles and to switch between mining whichever is the most profitable between a regular block and a Flashbots block. 
Find out more about MEV-Geth [here](https://github.com/flashbots/mev-geth).

### How much hashrate is currently running MEV-Geth?

There are 5 mining pools running MEV-geth, collectively accounting for over 12% of total Ethereum hashrate.

### What's MEV-Relay?

MEV-Relay is a hosted gateway which forwards bundles to mining pools who registered their MEV-geth nodes so that it's easier for you to reach all miners in one place. You can find the MEV-relay source code [here](https://bit.ly/390zf8b). 

### Why do I have to use MEV-Relay?

Using MEV-relay is required during the alpha to aggregate bundle requests from all users, prevent spam and DOS attacks on participating miner(s)/mining pool(s), and collect necessary system health metrics. We are working to remove this requirement in future releases of MEV-geth.


:warning: Please be aware that if you decide to use the relay to submit bundles, the operator of the relay is a trusted intermediary. :warning:  

### Can I send bundles to miners without going through the Relay?
Unfortunately not in Flashbots Alpha.

### Where are the Relay servers located?
They are currently in US-East-2 (Ohio) but we are thinking of turning them into a lambda that runs globally.


### What are examples of Flashbots transactions?
- Here is an example of a Flashbots bundle with a single transaction:
https://etherscan.io/tx/0x5e1657ef0e9be9bc72efefe59a2528d0d730d478cfc9e6cdd09af9f997bb3ef4


- Here is an example of a Flashbots bundle with 3 transactions:
    - tx #1: https://etherscan.io/tx/0xab16c4a7dbb403f45b8cd76945d25138d3c9728ece18959eb0c551d6653018d7
    - tx #2: https://etherscan.io/tx/0x89e47b1d61dbbaaee5669fe1dd783fa0564f380eda47df39e4053bccaa057714
    - tx #3: https://etherscan.io/tx/0x9683e160bdf8628c294bf999c874cddff37254eccc0e486dbe2271531b064178

![](https://hackmd.io/_uploads/Sy1Lj2pX_.png)
*Red line indicates where the bundle is*

### What level of transparency do you provide into how this infrastructure works?
MEV-Geth, MEV-Relay and all the code searchers interact with is open-source and documented on our [Github repo](https://flashbots.com). In addition we publish monthly [transparency reports](https://medium.com/flashbots/tagged/transparency-report).

We've also released a publicly accessible API blocks.flashbots.net for displaying Flashbots blocks and txs, and will be releasing live data visualizations useful to searchers in the coming weeks.


### Can I use Flashbots concurrently with any other similar systems mentioned above? 
Yes! As a searcher you want to maximize the hashrate you're exposed to and there is no reason you can't submit your trades to Flashbots and another system in parallel.

One could also imagine a dual system that submits txs to both the traditional Ethereum mempool and Flashbots, with bot logic conditional on one or the other landing.

### Why should I adopt Flashbots?
Flashbots is immediately useful to any searcher who has ever lost PGAs or lost money on unsuccessful trades that still cost gas.

In addition, we expect searcher adoption to increase in a lagging fashion relative to hashrate adoption. Being an early adopter means you'll be here during this lagging window where the tip paid to miners won't be as high as it will be when more traders onboard Flashbots.



### Will MEV-Geth eventually just maximize miners profits and minimize arbs profits?
It is possible but it at least won't be the case for the short to medium term.
 
 It's worth nothing we are already on the path of 100% mev extraction from miner. Gas price auctions are already giving more and more of the opportunity they're going after to miners. In fact, for many opportunities, the miner take is OVER 100% due to number of competitors and paying for failure.

### What is the likelihood of a transaction being executed in block X?
Assuming your bundle has been selected, your chance of inclusion is directly linked to the amount of hashrate running MEV-Geth.

eg. if 10% of hashrate is running MEV-Geth, then you have a ~10% chance of being mined (assuming your bundle is successful)

### What will happen when EIP1559 is released?
There won't be any significant change to the Flashbots system. Bundles that land on-chain will have to pay the `BaseFee`. 

We may build a BASEFEE provider to still allow empty EOA use of Flashbots vs having to pre-fill it with ETH to pay it.

### Is there any tooling available to searchers?
- Flashbots providers [Ethers.js](https://github.com/flashbots/ethers-provider-flashbots-bundle/blob/9e039cc92fcaa3d15e71f11faa7acf4f4f0674fa/src/index.ts#L307-L310) and [Web3.py](https://github.com/flashbots/web3-flashbots)
- [Simple arbitrage searcher codebase](https://github.com/flashbots/simple-arbitrage)
- Simulation tool for Flashbots bundles in past blocks
- blocks.flashbots.net API

 And more coming soon!

### Where can I submit a feature or tooling request?
In the Flashbots PM repo [Discussions section](https://flashbots.com/pm/discussions). 

## Implementation


### Do I need any sort of key to access the relay?

*Note: Initially, the Flashbots MEV-Relay was released with a required set of API Keys that needed to be provided with every relay interaction, if you have one of these keys, you no longer need to use it.*

MEV-relay expects payloads to be signed using a standard Ethereum private key. This relay signing address does not need to be given to Flashbots in advance, and it does not need to store any ETH or other assets (and we recommend it does not and it should be a different key from your bot's EOA key).

The signature needs to be provided via the 'X-Flashbots-Signature' Header. Reference implementation can be found in the [Flashbots Ethers Provider](https://github.com/flashbots/ethers-provider-flashbots-bundle/blob/9e039cc92fcaa3d15e71f11faa7acf4f4f0674fa/src/index.ts#L307-L310)

### Why is there rate limiting?
Rate limiting is currently in place to protect the infrastructure sitting behind it. Similar to how Infura has a gas limit on eth_call.


### Why do I need to provide this unrelated auth-signing key?

By signing payloads with your own relay signing key, this will enable building a reputation for high-priority delivery of your bundles to miners. MEV-relay simulates bundles before sending to miners which can take a small amount of time. MEV-relay cannot determine which bundles are profitable without performing a full simulation. This signing key allows the relay to infer which bundles are likely profitable, based on historical performance. Using a reputation system allows reliable searchers to be rewarded while still allowing new searchers to participate.

### How should I know the correct RPC that accepts eth bundle commands?
You have to set it to a regular Ethereum node.

### How do I target a timestamp range instead of a block number when submitting a bundle?

The best way to do this is to submit one bundle for each block in a range of blocks that is likely to contain the first block with a block.timestamp greater than the target timestamp.

You **do** need to submit a bundle per target block. You can re-submit the exact same signed transaction bundle; you don't need to re-sign. A more flexible API will be released soon to make this more efficient.

You can ALSO provide a minimum/maximum timestamp in the bundle, but this only provides a hint to discard the block if it falls outside this time range. It does not expand the target outside the single indicated block number, and it does not change the timestamp selected by the miner.

- 0,0 timestamp means no restriction

### Can I submit directly to the sendBundle method without using the library?
Please see the [reference implementation](https://github.com/flashbots/ethers-provider-flashbots-bundle), in particular how signing is done in order to be processed by the Relay.

### What is block.coinbase?
Block.coinbase is a standard Solidity function that gives the current block miner’s address. Read more about it [here](https://docs.soliditylang.org/en/v0.4.24/units-and-global-variables.html#block-and-transaction-properties).

### Do I need ETH in my account to pay block.coinbase to the Flashbots miner?
No, you can pay the miner with the profit of your bundle if it lands on-chain. This allows for account abstraction - something explained [here](https://github.com/flashbots/pm/issues/24) in further details.

### Can I pay my coinbase bribe in something else than ETH?
Unfortunately not at the current moment.

### Since this is a first-price sealed-bid auction, how do I know how much to bid for my bundle to win?
The lower bound on a successfully mined Flashbots bundle is the block tail gas price since the 'tail' of the block, and its transactions, will be pushed out to make room for a Flashbots bundle of transactions. This is what the miner's software will compare against when deciding whether to mine a Flashbots block or the vanilla Geth block.

If you have no competition for that block, then any tip above the lower bound will get you included (modulo hashrate). If other searchers are going for the same block, you have to pay the highest tip to be selected. We suggest you look at past data for other bids to get an idea of the average bid sizes.

### Will you implement a way for several non-overlapping bundles to be accepted within the same block?
Yes, we are actively working on bundle merging.

### Where can I get data on past auctions and past blocks?
blocks.flashbots.net

### When miners select bundles, are they differentiating between transfers to the coinbase and the gas fees to select which bundle is valid for inclusion?
Nope!

### Can I simulate my bundle against historical blocks to backtest them?
Yes, but only for dates after March 12th since the Relay is running with partial archive nodes. This means you can simulate blocks >= 12030000. This range will be extended shortly. 

### Can bundle simulations take into account state changes from an earlier transaction in the bundle? Eg. say first TX is buying the tokens, second is selling. 
Yep!

### Can I estimate gas used of the bundle beforehand?
Yes. You'll want to simulate with a 'fake' tip, like 1 wei, then see how much gas it uses, then change the tip.

### How does MEV-Geth work when it receives bundles?
Currently, MEV-Geth compares:
1. A normally constructed block.
2. Your bundle at the head of the block followed by a normally constructed everything else.
3. Alice's bundle at the head of the block, followed by a normally constructed block.
4. Bob's bundle at the head of the block, followed by a normally constructed block.
6. Carol's bundle at the head of the block, followed by a normally constructed block.
...

It then picks the block that results in the miner's balance increasing the most.

### Can I use a contract to tip ETH to the miner? 
You can pay miners either via gas or by sending ETH to their coinbase.
It's best to pay via block.coinbase transfer to prevent the inclusion of your bundle when you miss (i.e. you remove the miner incentive of inclusion on a miss) and to protect yourself from re-orgs.

### Can I deploy contracts using MEV?
Yes, you can pay the block.coinbase fee in the constructor or you can pay the block.coinbase fee in a separate tx after your contract creation.
Crucially, you don't need to deploy a new contract to include block.coinbase.transfer in the contract function, one already exists [FLashbotsCheckAndSend](https://etherscan.io/address/0xc4595e3966e0ce6e3c46854647611940a09448d3).

### Why didn't my transaction get included?
Unlike broadcasting a transaction and landing on-chain, even if the transaction fails, troubleshooting Flashbot bundles can be challenging, since transaction failure, incentives not being high enough, and a non-flashbot-miner all look the same: your transactions do not show up.

If using the library linked above, sending a bundle returns a promise that resolves to one of three outcomes:

1) Your bundle was included successfully on the target block
2) Your bundle became invalid due to one of the nonces inside the transaction becoming too low, either before or on the target block
3) Your bundle was not included, either because your coinbase payment was too low OR because the miner at the target block height was not a Flashbot miner

We recommend checking out this [great guide](https://fifikobayashi.medium.com/beginners-guide-to-troubleshooting-mev-on-flashbots-aee175048858) by Flashbots community member [Fiona Kobayashi](https://twitter.com/fifikobayashi) on issues searchers would have as they start sending Flashbots bundles. Fiona goes over a few reasons why your bundle might not be picked by miners:
* Noncompetitive gwei price
* Incorrect gas estimates
* Miner luck
* Outcompeted by another searcher
* Failing transaction
* Rate limiting
* Transaction nonce is too low


### What do I need to change in my bot aside from using the sendBundle to submit transactions?
To get the full benefit of using flashbots, it is beneficial to transition from transaction fee payment (e.g. `gasPrice * gasUsed`) to coinbase payments. Since you can now submit 0-gas-price transactions, you will need to add functionality to your on-chain code to pay `block.coinbase.transfer()` based on the reward intended for the miner. 

This can come from a calldata argument or some fixed percentage of the overall opportunity calculated on-chain. We recommend using calldata for specifying the reward in order to quickly react to fluctuations in flashbot bundle prices.

### Can I have a running bundle which I constantly update whenever I find a new trade? Essentially I want to continuously update my bunle until the next block arrives.
Your previous bundle is dropped if the new bundle is more valuable.










## Resources



### Other Resources
* Example arbitrage searcher: https://github.com/flashbots/simple-arbitrage
* MEV-Relay repo: https://github.com/flashbots/mev-relay-js
* Flashbots ethers.js provider: https://github.com/flashbots/ethers-provider-flashbots-bundle
* Flashbots web3.py provider: https://github.com/flashbots/web3-flashbots
* Ask any questions in the [#🤖searchers](https://discord.gg/d9XYzHA4hM) channel on our Discord 
* A technical overview of Flashbots the organization: https://ethresear.ch/t/flashbots-frontrunning-the-mev-crisis/8251
* Our values and what we stand for: https://medium.com/flashbots/frontrunning-the-mev-crisis-40629a613752
* [Flashbots: MEV Of The Week thread](https://twitter.com/epheph/status/1357089176898969600?s=20) by Scott Bigelow 
* [Lost ENS sanctuary using Flashbots](https://twitter.com/andrekorol1/status/1358252320207876104?s=19) by Andre Korol 🔥
* [The enemy of your enemy is NOT your friend](https://fiona.mirror.xyz/QXdCOAggA5g_j5R_JpO-V5LqK89EbimnYIV6c2rOsT0) by Fiona Kobayashi
* [Flashbots gasless transactions thread](https://twitter.com/amanusk_/status/1370642493621080071?s=20) by Alex Manuskin
* [Beginner's guide to troubleshooting MEV on Flashbots](https://fifikobayashi.medium.com/beginners-guide-to-troubleshooting-mev-on-flashbots-aee175048858) by Fiona Kobayashi
