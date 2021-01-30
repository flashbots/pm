# Flashbots

Flashbots is a research and development organization formed to mitigate the negative externalities and existential risks posed by miner-extractable value (MEV) to smart-contract blockchains, starting with Ethereum. Our primary focus is to enable a *permissionless, transparent, and fair ecosystem* for MEV extraction. Flashbots arose out of the *MEV Pi-rate Ship*, a neutral, chain-agnostic, interdisciplinary research collective that supports MEV-related theoretical and empirical research. 

Flashbots is committed to open source. The best way to learn about Flashbots is to follow our progress in each repo. Find out more about Flashbots organization in our [medium post](https://medium.com/flashbots/frontrunning-the-mev-crisis-40629a613752) _Flashbots: Frontrunning the MEV-crisis_.

Flashbots research and development are tightly-coupled dual engines that propel Flashbots in a phased approach:
- **Long-term oriented research effort:** They spec out and update our roadmap, define our organization’s phases and identify key milestones associated with each of them;
- **Milestone-oriented engineering effort:** They are organized as product-focused teams that ship core infrastructure and ecosystem tools, while collecting data and producing other artifacts that feed back into research.

![FlashbotsOrg](Flashbots_org.png)

## Flashbots Proof-of-concept (PoC)
</br> We have designed and implemented a proof of concept for permissionless MEV extraction called MEV-Geth. It is a sealed-bid block space auction mechanism for communicating transaction order preference. While our proof of concept has incomplete trust guarantees, we believe it's a significant improvement over the status quo. Find out more about Flashbots architecture and implementation in our [ethresearch post](https://ethresear.ch/t/flashbots-frontrunning-the-mev-crisis/8251).

## Flashbots Alpha

Flashbots Alpha is an early stage implementation of a direct communication channel between miners and Ethereum users that would like to communicate their preferred transaction order within a block.

Instead of broadcasting to the peer-to-peer network and competing with all Ethereum users, Flashbots users bid against each other in providing a reward directly to miners through a seal-bid auction. DeFi traders such as liquidators and arbitrageurs, can use Flashbots to bid for transaction inclusion without paying any gas fee for failed transactions, nor do they need to worry about leaking strategies before their trade was mined on-chain. 

Miners and/or mining pools can receive MEV revenue, which is expected to be higher than revenue from mining regular blocks, for including the most profitable transaction bundles submitted by Flashbots users.

Flashbots aim to provide a "direct fast lane" for transaction inclusion with an efficient, fair and sustainable MEV auction mechanism, potentially reducing network congestion and lowering transaction fees for regular Ethereum users.

We are currently in the Alpha phase of infrastructure development and are quickly iterating in public towards a permissionless infrastructure, as set forth in the [mission](https://medium.com/flashbots/frontrunning-the-mev-crisis-40629a613752) of Flashbots' organization. We welcome all ecosystem participants to provide early technical feedback via Github issues and pull requests in the relevant [repos](https://github.com/flashbots), create open-source toolings to improve transparency within Flashbots ecosystem, and participate in ongoing discussions on Flashbots' [Discord](https://discord.gg/3TjWjBerRb)!

### Flashbots Alpha is composed of MEV-geth & MEV-relay

Any miner and mining pool can run [MEV-geth](https://github.com/flashbots/mev-geth), a simple 500-line upgrade patch to the [geth](https://geth.ethereum.org/) codebase (see [summary](https://github.com/flashbots/mev-geth#what-is-the-difference-between-mev-geth-and-geth) of the [diff](https://github.com/ethereum/go-ethereum/compare/master...flashbots:master#diff-c426ecd2f7d247753b9ea8c1cc003f21fa412661c1f967d203d4edf8163da344R1970)) that Flashbots open-sourced in November 2020. MEV-geth opens the aforementioned communication channel on the miners' side. This Alpha release constitutes the opening of the other side of the communication channel: any Ethereum user can now send their transaction bundles to miners via MEV-relay. 

While anyone is able to send transaction bundles directly to miners who have chosen to publish their MEV-geth node RPC endpoint, Flashbots has implemented a hosted gateway called [MEV-relay](https://github.com/flashbots/mev-relay-js), which forwards bundles to the miner(s) and/or mining pool(s) who register their MEV-geth nodes. MEV-relay provides spam prevention for registered miner(s)/mining pool(s), and enables users to reach all miners registered on MEV-relay in one place.

### Onboard Flashbots Alpha as a Miner

If you are a miner and/or mining pool, we invite you to try Flashbots during this Alpha phase and start receiving MEV revenue by following these 4 steps:

1. Fill out this [form](https://forms.gle/78JS52d22dwrgabi6) to indicate your interest in participating in the Alpha and be added to the MEV-relay miner registry. 
2. You will receive an onboarding email from Flashbots to help [set up](https://github.com/flashbots/mev-geth/blob/master/README.md#quick-start) your MEV-geth node and protect it with a [reverse proxy](https://github.com/flashbots/mev-relay-js/blob/master/miner/proxy.js). 
3. Respond to Flashbots' email with your MEV-geth node RPC endpoint to be added to the MEV-relay gateway. 
4. After receiving a confirmation email that your MEV-geth node's endpoint has been added to the relay, you will immediately start receiving Flashbots transaction bundles with associated MEV revenue paid to you.

#### Resources to set up MEV-geth:

- MEV-geth repo: https://github.com/flashbots/mev-geth
</br> This is a fork of go-ethereum. Install this binary, your revenue will go up even if it reduces PGAs, it won't affect your mining operations otherwise. The diff is 500 lines, so it's dead simple for you DYOR and verify what we said is true.
- MEV-geth demo repo: https://github.com/flashbots/mev-geth-demo
</br> A quick start on running an MEV GETH node, and shows how a miner may profit from it by accepting MEV bundles either via direct block.coinbase smart contract "bribes", or with extra transactions that pay the block's coinbase if it's known ahead of time.
- Flashbots Miner FAQ: https://github.com/flashbots/pm/blob/main/guides/miner-onboarding.md
- Dedicated Flashbots Discord channel [#🤖mev-geth](https://discord.gg/neqxEYrA) .

### Onboard Flashbots Alpha as a Trader

If you are a DeFi trader or run bots, we invite you to test out Flashbots Alpha and start submitting MEV transaction bundles.

You can start submitting transaction bundles on mainnet by following these steps:
1. Join [#🤖searchers](https://discord.gg/KNFBvZzJyT) channel on Discord
2. Apply for an [API key](https://forms.gle/1uunzbhhZQP1BKtdA)
3. Update your searcher scripts to send bundles to `relay.flashbots.net` or directly to miners' RPC endpoints

#### Resources to write a MEV searcher:

- Example arbitrage searcher: https://github.com/flashbots/simple-arbitrage
- Flashbots ethers.js provider: https://github.com/flashbots/ethers-provider-flashbots-bundle
- Searcher FAQ: https://github.com/flashbots/pm/blob/main/guides/flashbots-alpha.md
- Dedicated Flashbots Discord channel [#🤖searchers](https://discord.gg/KNFBvZzJyT).

See you on chain ! :zap::robot: 


## Contribute to MEV-Research

We invite you to review our [MEV-Research](https://github.com/flashbots/mev-research) repo to learn about our MEV Fellowship program. Start contributing through opening or answering a Github issue, and/or writing a Flashbots Research Proposal (FRP) to co-author our research papers and/or apply for a research grant, and join our discussion on our [MEV-Research discord](https://discord.gg/9yuuZendEy) community.

## Join the MEV Roast!

MEV Roast is a semi-monthly discussion that stems from the research collective MEV Pi-rate Ship tradition. It's meant to service as a communication bridge between Flashbots and the broader community interested in understanding MEV and contribute to our research and engineering efforts. The format of our Roast is meant to invite constructive criticism on the Flashbots efforts and to address open questions. 
| No. |Date | Roast Master | Agenda | Notes | Recording |
|:---|:---|:---|---|---|:---|
8 | Nov 25 | @danrobinson | [agenda](https://github.com/flashbots/pm/issues/1) | [slides](https://docs.google.com/presentation/d/1R0P-ypkiPJw-re0oKlkXkCF03UaVq_0fVnO9J0ewh1Q/edit?usp=sharing) | [video](https://drive.google.com/file/d/1bMwtTKtOITYr2DV_59RngOQADL4I3cWR/view?usp=sharing) |
9 | Dec 16 | Dan Elitzer | [agenda](https://github.com/flashbots/pm/issues/2) | [slides](https://docs.google.com/presentation/d/1fh-kdjnR-R6qpl3NE4wb6w2OAuMGBbSykKeCzM0ga6g/edit?usp=sharing) | [video](https://drive.google.com/file/d/1V2tJB6j7DVkQ9YKTShA5KACJNAk0kZje/view?usp=sharing) |
10 | Jan 13 | Tom Schmidt | [agenda](https://github.com/flashbots/pm/issues/3) | [slides](https://docs.google.com/presentation/d/1G-mzdBUnEODZnNBhy8yppF2LBImVy9-F39FFCeypoAA/edit?usp=sharing) | [video](https://drive.google.com/file/d/1s7L4vToP5v5_j5rpqES8CYzyF7mOto5Z/view?usp=sharing) |
11 | Jan 28 | Sunny Aggarwal | [agenda](https://github.com/flashbots/pm/issues/6) | [slides](https://docs.google.com/presentation/d/1tvyDza7svyShgClosvAshg6qgs0LDIWiYwrPKGphnqQ/edit?usp=sharing) | [video](https://drive.google.com/file/d/1_4-E_i6WIDMNRDIgBIf0YiaJtm33XW9s/view?usp=sharing) |


### Subscribe to MEV Ship Calendar

You can catch up on our latest community events by subscribing to the [MEV Ship Calendar](https://calendar.google.com/calendar/u/0/embed?src=c_7lurqn12ecl64li0ms3kse5vok@group.calendar.google.com): join us on our semi-monthly community call _MEV Roast_, our monthly Flashbots core dev call, weekly research workshop, and the upcoming unconference: MEV.wtf
