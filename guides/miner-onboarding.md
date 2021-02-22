# Flashbots Alpha - Miner Onboarding

*A collection of relevant information for miners looking to participate in Flashbots Alpha. This is a living document that will be continuously updated as we receive more questions from you.*

## Contents
1. [Executive Summary](#Executive-Summary)
2. [FAQ](#Frequently-Asked-Questions)
3. [Resources](#Resources)

## Executive Summary

### What is the Flashbots Alpha?

Flashbots Alpha is a proof-of-concept implementation of a direct communication channel between miners and Ethereum users that would like to communicate their preferred transaction order within a block.

Instead of broadcasting to the peer-to-peer network and competing with all Ethereum users, Flashbots users bid against each other in providing a reward directly to miners through a seal-bid auction. DeFi traders such as liquidators and arbitrageurs, can use Flashbots to bid for transaction inclusion without paying any gas fee for failed transactions, nor do they need to worry about leaking strategies before their trade is mined on-chain. 

Miners and/or mining pools can receive MEV revenue, which is expected to be higher than revenue from mining regular blocks, for including the most profitable transaction bundles submitted by Flashbots users.

Flashbots aim to provide a "direct fast lane" for transaction inclusion with an efficient, fair and sustainable MEV auction mechanism, potentially reducing network congestion and lowering transaction fees for regular Ethereum users while increasing revenues for miners.

We are currently in the Alpha phase of infrastructure development and are quickly iterating in public towards a permissionless infrastructure, as set forth in the [mission](https://medium.com/flashbots/frontrunning-the-mev-crisis-40629a613752) of Flashbots' organization. We welcome all ecosystem participants to provide early technical feedback via Github issues and pull requests in the relevant [repos](https://github.com/flashbots), create open-source toolings to improve transparency within Flashbots ecosystem, and participate in ongoing discussions on Flashbots' [Discord](https://discord.gg/3TjWjBerRb)!

### Flashbots Alpha is composed of MEV-geth & MEV-relay

Any miner and mining pool can run [MEV-geth](https://github.com/flashbots/mev-geth), a simple patch on the [geth](https://geth.ethereum.org/) codebase (see [summary](https://github.com/flashbots/mev-geth#what-is-the-difference-between-mev-geth-and-geth).

The patch is broken down into three modules:
1. bundle worker and `eth_sendBundle` rpc ([8104d5d7b0a54bd98b3a08479a1fde685eb53c29](https://github.com/flashbots/mev-geth/commit/8104d5d7b0a54bd98b3a08479a1fde685eb53c29) and [c2b5b4029b2b748a6f1a9d5668f12096f096563d](https://github.com/flashbots/mev-geth/commit/c2b5b4029b2b748a6f1a9d5668f12096f096563d))
2. profit switcher ([aa5840d22f4882f91ecba0eb20ef35a702b134d5](https://github.com/flashbots/mev-geth/commit/aa5840d22f4882f91ecba0eb20ef35a702b134d5))
3. `eth_callBundle` simulation rpc ([9199d2e13d484df7a634fad12343ed2b46d5d4c3](https://github.com/flashbots/mev-geth/commit/9199d2e13d484df7a634fad12343ed2b46d5d4c3) and [a99dfc198817dd171128cc22439c81896e876619](https://github.com/flashbots/mev-geth/commit/a99dfc198817dd171128cc22439c81896e876619))

While only the bundle worker and `eth_sendBundle` module is necessary to mine flashbots blocks, we recommend also running the profit switcher module to guarantee mining rewards are maximized. The `eth_callBundle` simulation rpc module is not needed for the alpha.

You can see a summary of all diffs [here](https://github.com/ethereum/go-ethereum/compare/master...flashbots:master).

To start receiving flashbots bundles from users, the miner needs to set a [reverse proxy](https://github.com/flashbots/mev-relay-js/blob/master/miner/proxy.js) to open their `eth_sendBundle` rpc and request to be whitelisted on the Flashbots hosted gateway called [MEV-relay](https://github.com/flashbots/mev-relay-js). MEV-relay is needed during the alpha to aggregate bundle requests from all users, prevent spam and DOS attacks on participating miner(s)/mining pool(s), and collect necessary system health metrics.

### Onboard Flashbots Alpha as a Miner

We invite you to try Flashbots during this Alpha phase and start receiving MEV revenue by following these 4 steps:

1. Fill out this [form](https://forms.gle/78JS52d22dwrgabi6) to indicate your interest in participating in the Alpha and be added to the MEV-relay miner whitelist. 
2. You will receive an onboarding email from Flashbots to help [set up](https://github.com/flashbots/mev-geth/blob/master/README.md#quick-start) your MEV-geth node and protect it with a reverse proxy. 
3. Respond to Flashbots' email with your MEV-geth node endpoint to be added to the MEV-relay gateway. 
4. After receiving a confirmation email that your MEV-geth node's endpoint has been added to the relay, you will immediately start receiving Flashbots transaction bundles with associated MEV revenue paid to you.

Here are the resources currently available to help you setup MEV-geth:
- MEV-geth repo: https://github.com/flashbots/mev-geth
- MEV-geth demo repo: https://github.com/flashbots/mev-geth-demo
- Dedicated Discord channel [#🤖mev-geth](https://discord.gg/neqxEYrA) where you can ask any questions

See you on chain ! :zap::robot: 

## Frequently Asked Questions (FAQs)

### 1. What is MEV? 
MEV stands for [Maximal Extractable Value](https://github.com/flashbots/mev-research/issues/24) (previously defined as [Miner Extractable Value](https://www.youtube.com/watch?reload=9&v=vR1v7AQ8i3k&ab_channel=IEEESymposiumonSecurityandPrivacy)). On any smart-contract blockchain, MEV is the maximal value that can be permissionlessly extracted from transaction ordering. This includes both 'basic' value such as transactions fees and block rewards, as well as 'advanced' value such as any kind of arbitrary inclusion, exclusion and re-ordering of transactions. On Ethereum today, miners have the most permissionless power with respect to transaction ordering. MEV exists on both layer 1 and layer 2 architecture, and will still exist in the future regardless of whether [EIP-1559](https://eips.ethereum.org/EIPS/eip-1155) gets implemented, as well as after Ethereum shifts to [2.0](https://ethereum.org/en/eth2/). 

In order to differentiate between the theoretical maximum extractable and what _is_ extracted from transaction ordering, we introduce the term REV - Realized Extractable Value to denote the value that _has been_ extracted as opposed to the theoretical maximal value (MEV) that _could have been_ extracted in a given block from the ordering of transactions. Most REV is captured today by DeFi traders, miners profit from it via the transactions fees these traders pay for their transactions to be prioritized.

### 2. How much MEV revenue has been extracted on Ethereum?

According to preliminary results from our [data collection and processing efforts](https://github.com/flashbots/mev-inspect-rs), a conservative estimate of REV, the total value extracted from transaction ordering on Ethereum since February 2020 is worth over $124 million. This figure accounts for revenue captured by traders who deployed common MEV strategies on DeFi protocols, and gas fees spent on these trades that went to miners who ended up mining the blocks with such MEV strategies. We plan to release more statistics and metrics on MEV extraction activities on Ethereum through our public dashboard 'MEV-explore' in the near future. 

![](https://hackmd.io/_uploads/Hk825A90w.png)

(_Note: these are preliminary results we plan to open-source and publish. As we expand our data collection efforts to further increase our coverage of major MEV-strategies, we expect REV measured to be significantly higher._)

### 3. What is the revenue upside from running Flashbots Alpha?
 
We estimate the MEV opportunity to be worth at least 0.19 ETH per block today, and expect it to grow as more MEV strategies get deployed and more DeFi protocols enter the space. Early Flashbots Alpha adopters will be able to enjoy first-mover advantage and capture a significant margin of the revenue generated by traders.

MEV revenue scales with the volume of users. The more users send transaction bundles via MEV-relay to miners who run MEV-geth, the more MEV revenue will go to miner(s) and/or mining pool(s).

**Case study transaction:**
https://etherscan.io/tx/0xf8333db853d92fd3a26a05a4b524146773ddbfe1bbc32a800efec32ea7eebb36

This was a 10.2 ETH arbitrage opportunity between two decentralized exchanges. [The trader](https://etherscan.io/address/0x94d6522c80fb261c147a4111ad9a49407042f9b4) paid the miner 1.8 ETH through regular transaction fees in order to be the one to secure this opportunity and profited 8.4 ETH from it. This adds up to 18% of the transaction value going to the miner and 82% to the trader.

Our open-source arbitrage trading bot was able to identify this same opportunity and submitted a Flashbots bundle for it to a miner running MEV-geth. Unfortunately, the miner didn't win the relevant block and the strategy went to the trader mentioned above. Had the Flashbots bundle been mined and because our bot is currently set to give 100% to miners, the miner running MEV-geth would've made 10.2 ETH or 5.6x more!

This is purely anecdotal as we don't expect Flashbots users to give 100% of their profits to miners. Yet, by having open-sourced this MEV strategy, it means anyone can replicate it anytime, thus creating more competition amongst the traders who will increase the % they give to miners in order to be included. Because transaction bundle submission on Flashbots does not incur gas fee for failed transactions, traders are incentivized to act as an MEV searcher and submit the same strategy on Flashbots as a seal-bid auction. 

### 4. What is Flashbots? How does it plan to make money?

Flashbots is a [research and development organization](https://github.com/flashbots/pm) formed to mitigate the negative externalities and existential risks posed by miner-extractable value (MEV) to smart-contract blockchains. We are building a permissionless, transparent, and fair ecosystem for MEV extraction to reinforce the Ethereum ideals. Read more about our organization and public commitments [here](https://medium.com/flashbots/frontrunning-the-mev-crisis-40629a613752).

Flashbots Alpha is an open-source software that does not extract any fees. That being said, fair and sustainable MEV revenue distribution across ecosystem stakeholders is a core area of study that our [research efforts](https://github.com/flashbots/mev-research) are actively looking into.  

### 5. Why MEV-geth?

Transaction inclusion priority and private mempool services become more valuable every day that Ethereum’s DeFi ecosystem grows. The current method to express transaction priority is through gas. Miners are the entity that have the most permissionless control over transaction inclusion and their mempool. We believe there exists a gap between miners and users.

MEV-geth bridges that gap. It allows miners to satisfy the user’s need, in exchange for additional revenue, on top of their pre-existing Ethereum transaction fees and block reward. This is done by outsourcing part of the mempool’s transaction selection and ordering process to third-party MEV-searchers who want to include their transactions as soon as possible (e.g. arbitrageurs).


### 6. What are the benefits of running MEV-geth vs. partnering directly with a trader (bot operator)?

From a short-to-medium term standpoint, we believe the revenue source from a diverse pool of high-quality traders will outweigh any bespoke fee-sharing deal with an individual trader both in size and in stability:

Flashbots provides open access for anyone to become an MEV searcher to submit transaction bundles. The sealed-bid auction mechanism of Flashbots that provides pre-trade privacy is inducive to attracting sophisticated traders who are competitive in the MEV game. From the miners' perspective, this enables profiting from a broad range of high-quality MEV opportunities that cannot be conceivably covered by a single bot operator. This is not only relevant for profitability but also for stability of your revenue: DeFi trading strategies become efficient very quickly and traders will need to constantly preserve their competitive edge by finding new strategies, it is much more likely that the blended average competitive edge of our pool of traders will be consistently higher than of a single trader.

From a longer-term standpoint, by running MEV-geth and being a market participant you are contributing to the Flashbots network effect:
more hashrate running MEV-geth leads to more MEV-search activity which leads to more MEV revenue for miners, which leads to more hashrate running MEV-geth!


### 7. What are the main differences between MEV-geth vs. geth?

MEV-geth is a fork of geth that adds an ability to include a transaction bundle at the beginning of each block via a new `eth_sendBundle` RPC call. You can see a summary of the differences between MEV-geth and geth [here](https://github.com/flashbots/mev-geth/blob/master/README.md#what-is-the-difference-between-mev-geth-and-geth), and a detailed list of changes by inspecting the [diff](https://github.com/ethereum/go-ethereum/compare/master...flashbots:master#diff-c426ecd2f7d247753b9ea8c1cc003f21fa412661c1f967d203d4edf8163da344R1970).


### 8. How does MEV-geth technically guarantee a net positive outcome for miners?

MEV-geth [compares](https://github.com/flashbots/mev-geth/blob/master/miner/worker.go#L1211) a regular block template vs. a template with Flashbots transaction bundle inserted in it. It will only mine the block template that includes the bundle if it results in higher miner revenue.

The Flashbots transaction bundle that outcompetes the least profitable transaction(s) at the bottom of a block will be included. If the Flashbots transaction bundles are not as profitable to include comparing to regular transactions, MEV-geth will default to regular block template. Thus the payout of mining on MEV-geth will be strictly more profitable than mining on geth, holding all else constant. 


### 9. When I run MEV-geth, how can I know for sure any additional revenue comes from MEV, rather than normal variance from mining?
The are a couple ways to see revenue that results from MEV:
* Transactions at the beginning of a block that send ETH to mining pool address;
* Searching MEV-geth logs: `grep Flashbots bundle`. 
    Results will look like `Flashbots bundle bundlePrice=48471302632  bundleLength=1`. 
    Note: the `bundlePrice` here is an adjusted gas price, not actually ETH.


### 10. How does MEV revenue work with existing mining pool reward systems, in particular Pay Per Share (PPS) vs. Pay Per Last (luck) N Shares (PPLNS)?

Dominant mining pool reward systems today has emerged over 10 years ago when pooled mining emerged on Bitcoin network. 
MEV opportunities present on smart contract blockchain, made available to miner(s) and/or mining pool(s) additional revenue source with different distribution from block rewards and transaction fees. 

Incumbent mining pools already in operation may continue to apply its existing reward system, i.e. PPS or PPLNS, by treating MEV revenue as part of the transaction fee in paying out the miners. 

Alternatively, we may see MEV mining pools forming, where distribution of MEV revenue takes into account the variance of MEV opportunities modified on top of existing mining pool reward systems.  


### 11. Does Flashbots run any simulation on how much money can be made by a miner if they were to run MEV-geth now?

Any miner listed on the Flashbots MEV-relay miner registry can track the MEV revenue on Flashbots network by examining the MEV-geth logs. Flashbots would like to work with ecosystem contributors to create open-sourced tooling and dashboards in order to improve visibility of the MEV extraction on the Flashbots ecosystem.


### 12. How long will it take for me to see significant revenue differences from MEV after I try Flashbots Alpha?

MEV opportunities are not distributed uniformly within blocks and are unpredictable, yet highly lucrative. Mining a block with Flashbots transaction bundle is a probability game with different distribution on top of proof-of-work mining. 

It depends on MEV searcher competition on Flashbots. The more competition amongst MEV searchers sending transaction bundles to miners running MEV-geth through MEV-relay, the more likely you will see significant revenue differences soon after you set up MEV-geth and join MEV-relay.

It also depends on % of your hashrate relative to the rest of the network. The more frequently you mine a block the more likely it is to include an MEV opportunity.

As Flashbots gains more users and DeFi continues to grow, you can expect seeing increased percentage of mining revenue coming from MEV.

### 13. If more mining pools adopt MEV-geth, would my potential revenue from MEV decrease due to competition?

No. Your revenue from MEV only depends on mining blocks that include an MEV opportunity. Other pools adopting MEV-geth don't impact your revenue as long your hashrate % stays constant. 

As more miners adopt MEV-geth we can expect more searcher becoming interested in submitting transactions via Flashbots driving increased searcher competition and more revenue for mining pools.

### 14. What is the impact of Flashbots on gas fees collected from normal transactions?

If the block is full, including a Flashbots transaction bundle will result in the lowest-priced transaction(s) to be pushed out of the bottom of the block. This will slightly reduce revenue component from gas but compensated by MEV revenue from the Flashbots bundle. 

Therefore, if a Flashbots transaction bundle is included, the miner revenue is always guaranteed to be higher than a regular block.


### 15. Has Flashbots done any load testing of MEV-geth? Where can I see the tests/results?
Load testing results: 
* 5 bundles at 12m gas each: 2-3s 
* 30 bundles at 3m gas each: ~9s 
* 15 bundles at 12m gas each: ~20s 

These tests were done on an AWS m5.xlarge running MEV-geth. In practice, this should not matter for miners, since Flashbots Alpha limits the bundles at the MEV-relay level.

### 16. Why should miner(s) and/or mining pool(s) use MEV-relay vs exposing its own MEV-geth node's RPC endpoint?

During Flashbots Alpha phase, we suggest miner(s) and/or mining pool(s) use MEV-relay, and do not expose RPC endpoint, for three reasons:

First, exposing your MEV-geth node's RPC endpoint without having taken the proper security measures opens you up to spam. We invest significant engineering resources into MEV-relay for spam prevention, scaling and other methods to ensure high reliability for MEV transaction bundle submitters.

Secondly, during Alpha phase bootstrapping MEV searcher activities, Flashbots is responsible for keeping the uptime guarantee for the MEV searcher network. Exposing endpoints directly will make your infrastructure susceptible to attack vectors such as DDoS and may put the entire Flashbots network at risk and breaks our obligations to the searchers, which may reduce the MEV revenue on the Flashbots network.

Lastly, during Alpha phase, we are collecting data on Flashbots network activities to improve Flashbots infrastructure. Therefore, we would prefer if more MEV transaction bundles are submitted through MEV-relay, which in turn will help us iterate towards next version. 

### 17. For a miner and/or mining pool running MEV-geth, is using MEV-relay strictly better than publishing endpoint directly?

During Alpha phase, we recommend that a miner and/or mining pool only whitelists MEV-relay IP address to receive RPC calls from it. MEV-relay implements spam prevention, rate limiting, bundle simulation and queuing to ensure high reliability for the network. In the future, Flashbots plans to iterate towards a decentralized approach to solving this problem through improving the design of MEV-relay.

If a pool chooses to expose their MEV-Relay endpoint publicly they are responsible for spam/DoS prevention. 

_Note: continuous degraded performance of miner(s) and/or mining pool(s)' endpoint may result in its temporary exclusion from the network._

### 18. How does Flashbots plan to address HTTP inefficiency for high volume of bundles?

Future iterations of MEV-relay will implement WebSocket connection in order to improve performance of bundle submission.

### 19. Can miners/mining pools choose to include only part of a Flashbots transaction bundle submitted through Flashbots?

No, miner(s) and/or mining pool(s) running MEV-geth without modification cannot include only part of Flashbots transaction bundle. The current MEV-geth implementation includes entire transaction bundles. 

Since miner payoff depends on MEV capture strategy success, removing a transaction from the bundle is likely to result in MEV not being captured, resulting in 0 payoff and exclusion of such bundle from the block.  

### 20. How does Flashbots prevent malicious pool behavior in the system?

During Alpha phase, Flashbots monitors flow of transaction bundles via MEV-relay and compares them vs. on-chain record. If malicious behavior like front-running incoming bundles or selling access to incoming bundles is detected, the miner(s) and/or mining pool(s) may be excluded from MEV-relay.

### 21. Why shouldn't a miner collaborate with a trader directly by sharing Flashbots transaction bundle flow with them?

Collusion between miner(s) and mining pool(s) on sharing transaction bundle flow will provide asymmetric advantage to certain traders of the ecosystem and potentially drive the best MEV searchers away, because the auction is no-longer fair.

From a short-to-medium term standpoint, we believe the MEV revenue sourced from a diverse set of competitive traders will outweigh any bespoke fee-sharing deal with an individual trader both in revenue amount and in stability:

Flashbots provides open access for anyone to become an MEV searcher to submit transaction bundles. The sealed-bid auction mechanism of Flashbots that provides pre-trade privacy is inducive to attracting sophisticated traders who are competitive in the MEV game. 

From a miners' perspective, this enables you to profit from a broad range of high-quality MEV opportunities that cannot be conceivably covered by a single bot operator. 

This is not only relevant for profitability but also for stability of your revenue: DeFi trading strategies become efficient very quickly and traders will need to constantly preserve their competitive edge by finding new strategies, it is likely that the weighted average of Flashbots' traders competitive edge will be consistently higher than of a single trader.



## Resources

* A technical overview of Flashbots the organization: https://ethresear.ch/t/flashbots-frontrunning-the-mev-crisis/8251
* Our values and what we stand for: https://medium.com/flashbots/frontrunning-the-mev-crisis-40629a613752
* Example arbitrage searcher: https://bit.ly/3hGbDtk
* MEV-Relay repo: https://github.com/flashbots/mev-relay-js
* Flashbots ethers.js provider: https://bit.ly/2MpNFXI
* Searcher FAQ: https://bit.ly/2Xb3FiI
* Ask any questions in Flashbots Discord [#🤖-mev-geth](https://discord.gg/d9XYzHA4hM) Channel, or email us at info@flashbots.net

