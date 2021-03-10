# Flashbots Alpha

A collection of relevant information related to the Flashbots Alpha release and running bots using Flashbots. This document will be continuously updated as we receive more questions from you.

## Contents
1. [Announcement](#announcement)
2. [FAQ](#frequently-asked-questions)
3. [Resources](#resources)

## Announcement on Jan 6 (updated given changes to alpha)

To start off 2021 with a bang, Flashbots is entering Alpha today. 

You can start submitting transaction bundles on mainnet by following these steps:
1. Join [#🤖searchers](https://discord.gg/KNFBvZzJyT) channel on Discord
2. Update your searcher scripts to send bundles to `relay.flashbots.net` 

After successfully mining our first bundle in block [11550019](https://bit.ly/38ahRyC) and doing reliability testing over the following days, we are ready to open this proof of concept to the public for anyone to get their Ethereum transactions prioritized using Flashbots bundles. We've open sourced the simple arbitrage searcher @epheph#8354 built for testing [here](https://bit.ly/3hGbDtk). You should be able to run this searcher out of the box.

We've built a hosted gateway called `mev-relay` that receives bundles from searchers and forwards them to mining pools who registered their `mev-geth` nodes with Flashbots. Using MEV-relay is required during the alpha to aggregate bundle requests from all users, prevent spam and DOS attacks on participating miner(s)/mining pool(s), and collect necessary system health metrics. You can find the `mev-relay` source code [here](https://bit.ly/390zf8b). We are working to remove this requirement in future releases of mev-geth.

Please be aware that if you decide to use any relay to submit bundles, the operator of the relay is a trusted intermediary. They can see the content of the bundles and potentially censor or steal them. :warning: Only send bundles to a relay you trust! :warning:We encourage other trusted parties to build their own relays for Flashbots bundles in order to increase system redundancy.

Here are the resources currently available to help you write a searcher:
- Example arbitrage searcher: https://bit.ly/3hGbDtk
- Flashbots ethers.js provider: https://bit.ly/2MpNFXI
- Searcher FAQ: https://tinyurl.com/y5kpss7j
- Ask any questions in [#🤖searchers](https://discord.gg/KNFBvZzJyT)

See you on chain ! :zap::robot: 

## Frequently Asked Questions

### What is the Flashbots Alpha?

Flashbots has built a proof of concept of what is essentially an efficient communication channel between miners and Ethereum users. This channel allows Ethereum users to express their order preference directly to miners.

Miners run MEV-Geth, a modified version of Geth that opens the communication channel on their side. The alpha release today is the opening of the other side of the communication channel, Ethereum users can now send their transactions to miners' RPC endpoints.

You can read a lot more about why we've built this and the technical details of this PoC in our [EthResearch post.](https://ethresear.ch/t/flashbots-frontrunning-the-mev-crisis/8251)


### Why should I be interested?

This is interesting for any DeFi trader who is subject to gas competitions with other traders and mempool sniping. In addition, submitting transactions to miners costs nothing in Flashbots which saves traders a lot of money.

### What's a searcher?

A searcher is a Flashbots user that submits transaction(s) to be prioritized through the Flashbots infrastructure.

### How do I become a searcher and start submitting Flashbots bundles?
You can start using Flashbots by updating your trading bots to fit the Flashbots message format and send Flashbots bundles to `relay.flashbots.net`.

### How much hashrate is currently running MEV-Geth?

There is currently 1-10% of hashrate at any time running this alpha. This figure will be updated as we onboard more hashrate.

### What's MEV-Relay?

MEV-Relay is a hosted gateway which forwards bundles to mining pools who registered their mev-geth nodes so that it's easier for you to reach all miners in one place. You can find the mev-relay source code [here](https://bit.ly/390zf8b). Using MEV-relay is required during the alpha to aggregate bundle requests from all users, prevent spam and DOS attacks on participating miner(s)/mining pool(s), and collect necessary system health metrics. We are working to remove this requirement in future releases of mev-geth.

:warning: Please be aware that if you decide to use any relay to submit bundles, the operator of the relay is a trusted intermediary. They can see the content of the bundles and potentially censor or steal them. Only send bundles to a relay you trust. :warning: We encourage other trusted parties to build other relays for Flashbots bundles in order to increase system redundancy. 

### What sort of key do I need to access the relay?

Note: Initially, the Flashbots MEV-Relay was released with a required set of API Keys that needed to be provided with every relay interaction, if you have one of these keys, you no longer need to use it.

MEV-relay expects payloads to be signed using a standard Ethereum private key. This relay signing address does not need to be given to Flashbots in advance, and it does not need to store any ETH or other assets (and we recommend it does not and it should be a different key from your bot's EOA key).

The signature needs to be provided via the 'X-Flashbots-Signature' Header. Reference implementation can be found in the [Flashbots Ethers Provider](https://github.com/flashbots/ethers-provider-flashbots-bundle/blob/9e039cc92fcaa3d15e71f11faa7acf4f4f0674fa/src/index.ts#L307-L310)

### Why do I need to provide this unrelated auth-signing key?

By signing payloads with your own relay signing key, this will enable building a reputation for high-priority delivery of your bundles to miners. The relay simulates bundles before sending to miners which can take a small amount of time. The relay cannot determine which bundles are profitable without performing a full simulation. This signing key allows the relay to infer which bundles are likely profitable, based on historical performance. Using a reputation system allows reliable searchers to be rewarded while still allowing new searchers to participate.

### How do I target a timestamp range instead of a block number when submitting a bundle?

The best way to do this is to submit one bundle for each block in a range of blocks that is likely to contain the first block with a block.timestamp greater than the target timestamp.

You **do** need to submit a bundle per target block. You can re-submit the exact same signed transaction bundle; you don't need to re-sign. A more flexible API will be released soon to make this more efficient.

You can ALSO provide a minimum/maximum timestamp in the bundle, but this only provides a hint to discard the block if it falls outside this time range. It does not expand the target outside the single indicated block number, and it does not change the timestamp selected by the miner.

### Can I submit directly to the sendBundle method without using the library?

While the [ethers.js library](https://github.com/flashbots/ethers-provider-flashbots-bundle) we wrote makes interaction with this provider easy, it isn't required or complicated to call `eth_sendBundle` directly.

Instead of the usual
```
eth_sendRawTransaction(signedTx)
```

you call:
```
eth_sendBundle(
    [signedTx, (optional: signedTx2, signedTx3 )],
    exactTargetBlockNumber,
    minTimestamp [optional],
    maxTimestamp [optional]
)
```

### Why didn't my transaction get included?
Unlike broadcasting a transaction and landing on-chain, even if the transaction fails, troubleshooting Flashbot bundles can be challenging, since transaction failure, incentives not being high enough, and a non-flashbot-miner all look the same: your transactions do not show up.

If using the library linked above, sending a bundle returns a promise that resolves to one of three outcomes:

1) Your bundle was included successfully on the target block
2) Your bundle became invalid due to one of the nonces inside the transaction becoming too low, either before or on the target block
3) Your bundle was not included, either because your coinbase payment was too low OR because the miner at the target block height was not a Flashbot miner

### What do I need to change in my bot aside from using the sendBundle to submit transactions?
To get the full benefit of using flashbots, it is beneficial to transition from transaction fee payment (e.g. `gasPrice * gasUsed`) to coinbase payments. Since you can now submit 0-gas-price transactions, you will need to add functionality to your on-chain code to pay `block.coinbase.transfer()` based on the reward intended for the miner. This can come from a calldata argument or some fixed percentage of the overall opportunity calculated on-chain. We recommend using calldata for specifying the reward in order to quickly react to fluctuations in flashbot bundle prices.

## Resources

We recommend checking out this [great guide](https://fifikobayashi.medium.com/beginners-guide-to-troubleshooting-mev-on-flashbots-aee175048858) by Flashbots community member [Fiona Kobayashi](https://twitter.com/fifikobayashi) going over some of the issues searchers would have as they start sending Flashbots bundles. Fiona goes over a few reasons why your bundle might not be picked by miners:
* Noncompetitive gwei price
* Incorrect gas estimates
* Miner luck
* Outcompeted by another searcher
* Failing transaction
* Rate limiting
* Transaction nonce is too low


### Other Resources
* Example arbitrage searcher: https://github.com/flashbots/simple-arbitrage
* MEV-Relay repo: https://github.com/flashbots/mev-relay-js
* Flashbots ethers.js provider: https://github.com/flashbots/ethers-provider-flashbots-bundle
* Flashbots web3.py provider: https://github.com/flashbots/web3-flashbots
* Ask any questions in the [#🤖searchers](https://discord.gg/d9XYzHA4hM) channel on our Discord 
* A technical overview of Flashbots the organization: https://ethresear.ch/t/flashbots-frontrunning-the-mev-crisis/8251
* Our values and what we stand for: https://medium.com/flashbots/frontrunning-the-mev-crisis-40629a613752

