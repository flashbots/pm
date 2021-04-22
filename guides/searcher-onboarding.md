# Flashbots Alpha - Searcher Onboarding

Welcome searcher :zap:🤖! Here you will find a collection of relevant information for searchers looking to use Flashbots Alpha. This document will be continuously updated as we receive more questions from you.

<br/> _Updated: 19.04.2021. Check Flashbots Discord [#release](https://discord.gg/Z26a7e2x) channel for the latest releases._ 

## Contents
1. [Summary](#summary)
2. (Tooling)
3. [FAQ](#frequently-asked-questions)
    * [General](#general)
    * [Implementation](#implementation)
4. [Resources](#resources)

## Summary
### What is Flashbots Alpha?
Flashbots Alpha is a way for Ethereum users to directly communicate transactions to miners. We call users of Flashbots "searchers." That is because typically these users are *searching* for MEV - such as arbitrage or liquidation opportunities - and attempting to capture some of this MEV. Capturing MEV is typically highly dependent on executing transactions in a specific order, for example to capture the MEV from a liquidation you want to be the first person to transact after an oracle update. Flashbots allows searchers to specify the order in which they want transactions executed in a granular fashion through what we call "bundles." A bundle is simply one or several transactions "bundled" together. Either an entire bundle will be included in a block, or none of the bundle will. The ability to specify transaction ordering preference through bundles gives searchers a powerful tool for efficient capturing of MEV.

Another benefit of Flashbots Alpha is the ability to pay miners through a smart contract method called `block.coinbase.transfer()`, which transfers Ether to the miner of that block. By doing so users can send 0 gas price transactions where they only pay if their transactions are successful.

Flashbots Alpha is made of two components: MEV-Geth, a slightly modified fork of the Ethereum Geth client, and MEV-Relay, a transaction bundle relayer. As a searcher you will be submitting your bundles to MEV-Relay and they will be mined by MEV-Geth, but you don't have to run either of these. We will explain below precisely what you need to do to submit transactions through Flashbots.

### What are bundles?
Searchers use Flashbots to submit bundles to miners for inclusion in blocks. Bundles are one or more transactions that are grouped together and executed in the order they are provided. In addition to the searcher's transaction(s) a bundle can also potentially contain other users' pending transactions from the mempool, and bundles can target specific blocks for inclusion as well. Here's an example:

```
// Using the map below ships two different bundles, targeting the next two blocks
const blockNumber = await provider.getBlockNumber()
const minTimestamp = (await provider.getBlock(blockNumber)).timestamp
const maxTimestamp = minTimestamp + 120
const bundlePromises = [blockNumber + 1, blockNumber + 2].map((targetBlockNumber) =>
  flashbotsProvider.sendBundle(
    [
      {
        signedTransaction: SIGNED_ORACLE_UPDATE_FROM_PENDING_POOL // serialized signed transaction hex
      },
      {
        signer: wallet, // ethers signer
        transaction: transaction // ethers populated transaction object
      }
    ],
    targetBlockNumber, // block number at which this bundle is valid
    {
      minTimestamp, // optional minimum timestamp at which this bundle is valid (inclusive)
      maxTimestamp // optional maximum timestamp at which this bundle is valid (inclusive)
    }
  )
)
```

In the above example we've used constructed a bundle that includes our transaction (`transaction`) and a transaction from the mempool: `SIGNED_ORACLE_UPDATE_FROM_PENDING_POOL`. Furthermore, we've targeted our bundle for inclusion in the next two blocks.

### Paying miners through block.coinbase.transfer()

Another important thing about bundles is that they must include a payment to the miner made via `block.coinbase.transfer()`, which is a function in a smart contract. By using this function searchers can pay their transactions through a smart contract internal transaction without needing to pay normal gas fees. In other words, users can submit transactions that have a gas price of 0, and they can also make their payment to the miner conditional on some success criteria (e.g. they made money). Here's an example from our open source [simple arbitrage bot](https://github.com/flashbots/simple-arbitrage/blob/master/contracts/BundleExecutor.sol#L55-L73):

```    
function uniswapWeth(uint256 _wethAmountToFirstMarket, uint256 _ethAmountToCoinbase, address[] memory _targets, bytes[] memory _payloads) external onlyExecutor payable {
        require (_targets.length == _payloads.length);
        uint256 _wethBalanceBefore = WETH.balanceOf(address(this));
        WETH.transfer(_targets[0], _wethAmountToFirstMarket);
        for (uint256 i = 0; i < _targets.length; i++) {
            (bool _success, bytes memory _response) = _targets[i].call(_payloads[i]);
            require(_success); _response;
        }

        uint256 _wethBalanceAfter = WETH.balanceOf(address(this));
        require(_wethBalanceAfter > _wethBalanceBefore + _ethAmountToCoinbase);
        if (_ethAmountToCoinbase == 0) return;

        uint256 _ethBalance = address(this).balance;
        if (_ethBalance < _ethAmountToCoinbase) {
            WETH.withdraw(_ethAmountToCoinbase - _ethBalance);
        }
        block.coinbase.transfer(_ethAmountToCoinbase);
    }
```

The above smart contract code will attempt to capitalize on arbitrage opportunities. If it does not make money doing so then the transaction will fail. Moreover, since the searcher is paying the miner via `block.coinbase.transfer()` on the last line then the searcher won't pay any transaction fees.

### How do I submit bundles?
To submit bundles you must change the endpoint you're submitting to. Our endpoint is `https://relay.flashbots.net/`, and you can directly interact with it by using the JSON-RPC methods covered in [the MEV-Relay repo](https://github.com/flashbots/mev-relay-js) or you can use one of the wrappers that have been developed to make this process easier to integrate. There are both [Ethers.js](https://github.com/flashbots/ethers-provider-flashbots-bundle) as well as [web3.py](https://github.com/flashbots/web3-flashbots) providers, and a Go example can be found [here](https://hyegar.com/posts/flashbots-rpc/).

You can use your node or usual data provider (e.g. Infura, Alchemy, etc) as a source of data. There is no need to run MEV-Geth on the searcher's side.

### How much do I pay the miner?
**The most likely reason why your bundles aren't appearing on-chain is that you aren't paying the miner enough.** So how do you know how much to pay the miner. Unfortunately there isn't an easy answer to this question, but there are two high level considerations you should keep in mind.

The first consideration is that your bundle needs to be more profitable than the transactions it is displacing in a block. To be specific your bundle must beat out transactions that would have been included at the tail end of a block. These transactions have the least gas price, and your bundle must have a higher effective gas price than these as measured by the total coinbase payment of your bundle divided by the gas it consumes.

The second consideration is that your bundle needs to be more profitable than *other bundles* in order to be included. If another bundle has a higher effective gas price then that bundle will be prioritized. After Flashbots implements bundle merging then multiple bundles will be able to be merged at once, but for now only one bundle can be included in a block. In the meantime we recommend monitoring what other searchers are paying via the #bundle-alerts channel on Discord and [our API for Flashbots blocks](https://blocks.flashbots.net/).

### Is that all I need to get started?
Yep! To summarize you need three things:
- A way to construct and send bundles to Flashbots' MEV-Relay. We have developed easy ways to do this with [Ethers.js](https://github.com/flashbots/ethers-provider-flashbots-bundle) and [web3.py](https://github.com/flashbots/web3-flashbots). An example of how to construct and send bundles can be seen [here](https://github.com/flashbots/simple-arbitrage/blob/master/src/Arbitrage.ts#L138-L178). Alternatively you can refer to the [documentation here](https://github.com/flashbots/mev-relay-js/blob/e87f10cf44f4c0c67725ba0dc7274627b70cf478/README.md#eth_sendbundle) on how to use `eth_sendbundle` directly
- A smart contract to pay the miner via `block.coinbase.transfer()`. An example smart contract can be seen [here](https://github.com/flashbots/simple-arbitrage/blob/master/contracts/BundleExecutor.sol#L72).
- Transactions that are profitable for the miner to include in a block! Note: these don't *need* to capture MEV, they simply need to be more profitable to include than other transactions.

Please note that as a searcher you **do NOT** have to run MEV-Geth. Further, today you **MUST** submit your bundles to Flashbot's relay. There is no way to directly send bundles to a miner, although we are working on this.

If you want more resources before getting started:
* Simple-arbitrage-searcher example bot we've open-sourced here: [simple-arbitrage-searcher]()
* Dedicated [#🤖searchers](https://discord.gg/KNFBvZzJyT) channel on Discord!
* Full [list](#resources) of resources
* The FAQ just below this

See you on-chain ! :zap:🤖

## Tooling for searchers
- [Ethers.js Flashbots bundle provider:](https://github.com/flashbots/ethers-provider-flashbots-bundle) an Ethers.js provider that makes it simple to send bundles to Flashbots. Note that this provider also gives searchers a simple way to simulate transactions as well.
- [web3.py Flashbots bundle provider:](https://github.com/flashbots/web3-flashbots) an web3.py provider that makes it simple to send bundles to Flashbots. Note that this provider also gives searchers a simple way to simulate transactions as well.
- [MEV-Blocks:](https://blocks.flashbots.net/) an APi for displaying Flashbots blocks and transactions. Very useful for getting information on bundles that were included in blocks.
- [Example searcher codebase:](https://github.com/flashbots/simple-arbitrage) an example of how a searcher could work end to end from monitoring opportunities to executing on them, including the smart contract to do so. Please note that while this is an excellent learning resource this codebase has been deployed by dozens of people and is unlikely to be profitable anymore.

## Frequently Asked Questions (FAQs)
## General

### Who is behind Flashbots Alpha?

The Flashbots organization is behind Flashbots Alpha. We are a research and development organization working on solving the problems MEV causes to state-rich blockchains. You can find out more about the organization on our [pm repo](https://github.com/flashbots/pm) and in this introductory [Medium post](https://medium.com/flashbots/frontrunning-the-mev-crisis-40629a613752) that details our values and motives. 

### Can you give a step by step description of how Flashbots works for a searcher today?
* Searchers send Flashbots "bundles" to MEV-Relay. A bundle contains:
  * one or several transactions that can be the trader's and/or other users' pending transactions from the mempool
  * a sealed tip that is paid by the searcher to the miner via a smart contract call to `block.coinbase.transfer()`
* Moreover, bundles have these properties:
  * There can only be a single bundle per block (we are working on bundle merging to enable multiple bundles per block)
  * Flashbots bundles will always be at the top slot of the block
  * Bundles cannot be 'broken up' into multiple transactions. All transactions in a bundle must be included together.
* MEV-Relay receives bundles and sends them to all whitelisted miners running MEV-Geth
* Miners receive Flashbots bundles from MEV-Relay and process them in MEV-Geth
* MEV-Geth picks the most profitable bundle out of all bundles it is sent.
* MEV-Geth then compares the block that includes this bundle with a vanilla block that does not include any bundles. If it is more profitable to include a bundle MEV-Geth will do so, but otherwise it will default back to a vanilla Geth block.
* Only when the a searcher's bundle is included in a block is the tip associated with their bundle paid.
  * If a bundle is not included it does not cost the searcher anything (i.e. no gas fees are paid for failed transactions)

### Why use Flashbots Alpha?
* It allows searchers to bypass the Ethereum mempool and avoid their strategy leaking before it is mined on-chain (e.g. being frontrun by generalized frontrunners).
* It allows searchers to save money from avoiding paying gas fees for failed transactions.
* It allows miners to receive additional revenue in the form of the bundle tip in exchange for including the most profitable bundle in the block they mined.
* It reduces Ethereum network congestion and lowers Ethereum network transaction fees.

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

There are 5 mining pools running MEV-geth, collectively accounting for over 50% of total Ethereum hashrate.

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
Flashbots is immediately useful to any user who has ever lost priority gas auctions ("PGAs") or lost money due to unsuccessful trades that still cost gas.

### Will MEV-Geth eventually just maximize miners profits and minimize searcher profits?
It is possible but it at least won't be the case for the short to medium term.
 
It's worth nothing we are already on the path of 100% mev extraction from miner. Gas price auctions are already giving more and more of the opportunity they're going after to miners. In fact, for many opportunities, the miner take is OVER 100% due to number of competitors and paying for failure.

### What will happen when EIP1559 is released?
There won't be any significant change to the Flashbots system. Bundles that land on-chain will have to pay the `BaseFee`. 

We may build a BASEFEE provider to still allow empty EOA use of Flashbots vs having to pre-fill it with ETH to pay it.

### Where can I submit a feature or tooling request?
In the Flashbots PM repo [Discussions section](https://flashbots.com/pm/discussions). 

### Do I need any sort of key to access the relay?

*Note: Initially, the Flashbots MEV-Relay was released with a required set of API Keys that needed to be provided with every relay interaction, if you have one of these keys, you no longer need to use it.*

MEV-relay expects payloads to be signed using a standard Ethereum private key. This relay signing address does not need to be given to Flashbots in advance, and it does not need to store any ETH or other assets (and we recommend it does not and it should be a different key from your bot's EOA key).

The signature needs to be provided via the 'X-Flashbots-Signature' Header. Reference implementation can be found in the [Flashbots Ethers Provider](https://github.com/flashbots/ethers-provider-flashbots-bundle/blob/9e039cc92fcaa3d15e71f11faa7acf4f4f0674fa/src/index.ts#L307-L310)

### Why is there rate limiting?
Rate limiting is currently in place to protect the infrastructure sitting behind it. Similar to how Infura has a gas limit on eth_call.

### Why do I need to provide this unrelated auth-signing key?
By signing payloads with your own relay signing key, this will enable building a reputation for high-priority delivery of your bundles to miners. MEV-relay simulates bundles before sending to miners which can take a small amount of time. MEV-relay cannot determine which bundles are profitable without performing a full simulation. This signing key allows the relay to infer which bundles are likely profitable, based on historical performance. Using a reputation system allows reliable searchers to be rewarded while still allowing new searchers to participate.

### Are you on any testnets?
Yes, we are on Goerli. Our endpoint is `https://relay-goerli.flashbots.net/`.

### How should I know the correct RPC that accepts eth bundle commands?
You have to set it to a regular Ethereum node. You do not need to run MEV-Geth as a searcher.

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
