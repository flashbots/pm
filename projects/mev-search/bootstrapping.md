---
tags: project-plan
---

# mev-search: interface, libraries, tiny arb bot

[![hackmd-github-sync-badge](https://hackmd.io/UWLA13c-ROGmPlWNFCujdA/badge)](https://hackmd.io/UWLA13c-ROGmPlWNFCujdA)


| Project    | Phase | Deadline | Navigator | Description |
| ---------- | ----- | -------- | --------- | ----------- |
| mev-search | developer-friendly-tooling   | Jan 1      | @epheph   | Publish libraries and initial searcher to support bot-operators kickstart marketplace          |

## motivation
Flashbots benefits from as much searcher volume as possible. By publishing and promoting Flashbot-specific libraries, we can accelerate the adoption process of searchers, ensuring the virtuous cycle of hashrate.

As with any marketplace, it is important to kick-start one side of it, and merely providing the tooling to ease integration is not quite enough. This phase also includes writing, open sourcing, and operating a basic, but profitable, arbitrage bot targetting small opportunities that are often ignored by larger bots, due to risk.

## context for library
For most rpc endpoints, Ethereum libraries use extremely simple function->rpc call pass throughs, with basic typing, validation, and error handling. One rpc endpoint stands out from the others: `eth_sendTransaction`. Functions that wrap `eth_sendTransaction` usually offer important and useful high-level functionality:
* Signing the transaction with a private key
* Returning the hash
* Verifying submission to endpoint
* Providing functions for wait()'ing for the transaction time to mine
    * Success: return transaction receipt and logs
    * Transaction Failure: Throw error with failure details
    * Other failures: nonce replacement, timeout, etc

`eth_sendBundle` is essentially a superset of `eth_sendTransaction`, but with silent error scenarios. Let's make integrating with the new `eth_sendBundle` as pleasant and feature-rich while surfacing as many errors as possible. 


## context for initial searcher
There are several profitable opportunities that have positive EV only if executed within Flashbots. One such opportunity is "small" arbitrage, where gas fees could consume 85% or more of the total profit. These are likely to be ignored by "normal" bots, but are still MEV that is not being captured.

To accelerate the delivery date of this initial searcher, and to keep the bot software readable , it will focus exclusively on 2-hop arbitrage (e.g. ETH->DAI->ETH) between the following DEXs, implemented in this order: 
1. Uniswap V2
2. Uniswap V2 clones
3. Curve
4. Bancor or Balancer

There are two types of DEX's we will avoid for this arb searcher:
* off-chain signed-orders (e.g. 0x)
* DEXs with price change over time (such as mooniswap, since we will not know the timestamp of the block and are avoiding all staticcall's)


## timeline
* December 11: Functional first library published
* December 21: Running, viable "small" arbitrage searcher
* January 1: Improved, open sourced small arbitrage searcher

## objective
* Identify popular programming languages and Ethereum libraries used in bot operation
* Identify requirements from existing Ethereum developers and bot operators
* Based on above identifications, design interface and feature-set that plugs into these libraries to provide developers with commonly needed functionality when submitting to `eth_sendBundle`
* Develop, document, and publish flashbots libaries
* Integrate newly-published library into open source arbitrage bot
* Promote these libraries to potential searchers

## deliverables
* Survey to be given to bot operators inquiring about language, libraries, and desired feature-set: [Survey Document](https://docs.google.com/forms/d/e/1FAIpQLSe7Jcoy36ebxozvnK07OUzN-jtmo2PD9xc0pANUYQVt8Y-e4Q/formResponse)
* From survey, curate list of popular programming languages and Ethereum libraries to target
* From survey, create interface and specification for interacting with `eth_sendBundle`
* Libraries published to their various langauge-specific repositories (GitHub + (npm, PyPi, etc)) 
  -  TypeScript module: [@flashbots/ethers-provider-bundle](https://github.com/flashbots/ethers-provider-flashbots-bundle)
* Functional, open sourced arbitrage published and operated

## specification
To be fleshed out and altered based on survey results, initial functionality:
* Signing a mixed array of transaction requests and pre-signed transactions
* Control flow based on waiting for transaction completion or exclusion
* Simulate transactions that were not included for troubleshooting
* Parsing competitor's completed flashbots bids

## team dependencies
* Everyone - Help promote developer survey to bot operators
* mev-geth
  - Providing eth_sendBundle, making tweaks to its parameters and response as needed (e.g. adding calculated "bundle hash" in rpc response (like `sendTransaction` does))
  - Updating transaction simulation to use arbitrary blockNumber + timestamp against specific storage states (perfect simulation) [mev-geth issue: Transaction Simulation](https://github.com/flashbots/mev-geth/issues/9)
* mev-inspect - mev-search should, using perfect transaction simulation, provide data to mev-inspect for charting searched profit, even if transaction was not mined
    * Example: ![](https://hackmd.io/_uploads/SJcvQMhsD.png)


## process
* Weekly call
* Discord #mev-search