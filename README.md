# Flashbots

Flashbots is a research and development organization working on mitigating the negative externalities of current MEV extraction techniques and avoiding the existential risks MEV could cause to state-rich blockchains like Ethereum. Our primary focus is to enable a permissionless, transparent, and fair ecosystem for MEV extraction. It falls under three goals: Democratizing Access to MEV Revenue, Bringing Transparency to MEV Activity and Redistributing MEV Revenue.

We've released two projects so far: 
* Flashbots Alpha: a proof of concept communication channel between miners and searchers for transparent and efficient MEV extraction.
* MEV-Explore: a public dashboard and live transactions explorer of MEV activity.

You can interact with Flashbots on Discord and Github. We have dedicated channels on Discord for each of our efforts and welcome contributions. Our work is open source and you can follow our progress in each repository of the Flashbots organization. In addition, we hold several recurring community calls: a monthly MEV Roast, a bi-weekly research workshop and a monthly transparency call to update the community on our progress. 

The current repository you're in provides an overview of our activities, and contains our [open job searches](https://github.com/flashbots/pm/tree/main/jobs) and the [onboarding documents](https://github.com/flashbots/pm/tree/main/guides) to start using the Alpha we've released, either as a miner or as a searcher.

Learn more about our values and our mission: [Flashbots - Frontrunning the MEV Crisis](https://medium.com/flashbots/frontrunning-the-mev-crisis-40629a613752)
<br> Join us on Discord: [#Flashbots](https://discord.gg/7hvTycdNcK)
<br> Subscribe to our public calendar [here](https://bit.ly/3uJAHpo)

### Index:
* [Introduction](https://github.com/flashbots/pm#flashbots)
* [Flashbots Alpha](https://github.com/flashbots/pm#flashbots-alpha)
* [MEV-Explore](https://github.com/flashbots/pm#mev-explore)
* [Our organization & ways you can contribute](https://github.com/flashbots/pm#our-organization)
* [List of resources, MEV Roasts recordings and how to navigate Flashbots' github](https://github.com/flashbots/pm#resources)

----
## Flashbots Alpha

On Jan 6th 2021, we entered the Flashbots Alpha phase by establishing a proof-of-concept communication channel between traders (searchers) and miners. This proof-of-concept is made of two components: MEV-Geth, a slightly modified fork of the Ethereum Geth client, and MEV-Relay, a transaction bundle relayer.

**How it works:**
* Searchers send Flashbots "bundles" to MEV-Relay. A bundle contains:
  * one or several transactions that can be the searcher's and/or other users' pending transactions from the mempool
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

This infrastructure allows:
* searchers to bypass the Ethereum mempool and avoid their strategy leaking before it is mined on-chain (i.e. being frontrun by generalized frontrunners)
* searchers to save money from avoiding to pay gas fees for failed transactions.
* miners to receive additional revenue in the form of the bundle tip, in exchange for including the most profitable bundle in the block they mined.

Interested in learning more?
<br> [Onboard Flashbots alpha as a searcher](https://github.com/flashbots/pm/blob/main/guides/searcher-onboarding.md)
<br> [Onboard Flashbots alpha as a miner](https://github.com/flashbots/pm/blob/main/guides/miner-onboarding.md)
<br> [Jan 2021 Transparency Report](https://medium.com/flashbots/flashbots-transparency-report-january-2021-922514de8b8a)
<br> [Feb 2021 Transparency Report](https://medium.com/flashbots/flashbots-transparency-report-february-2021-8ac45b467d0a)
<br> [Mar 2021 Transparency Report](https://medium.com/flashbots/flashbots-transparency-report-march-2021-d3930b4b98a9)

We are quickly iterating in public towards a permissionless infrastructure with full transaction privacy. We welcome all ecosystem participants to provide early technical feedback via Github issues and pull requests in the relevant [repos](https://github.com/flashbots), create open-source toolings to improve transparency within Flashbots ecosystem, and participate in ongoing discussions on Flashbots' [Discord](https://discord.gg/3TjWjBerRb)!

## MEV-Explore

On Jan 22 2021, we released [MEV-Explore v0](https://explore.flashbots.net) a public dashboard and live transaction explorer for MEV transactions. 

How much MEV has been extracted on Ethereum? How much MEV do DeFi traders and bot operators capture? How much gas was spent on MEV transactions that went to the miners who mined the proof of work solutions with the MEV transactions? What are the most common types of MEV extraction strategies? Which DeFi protocols contain the most MEV? How much network resources do successful and failed MEV transactions take up?

With these questions in mind, we scraped Ethereum, covering over 8 major DeFi protocols and their clones, and collected more than 1.3 million MEV transactions that landed on-chain since Jan 1 2020 to provide a preliminary lower-bound to Extracted MEV. With the release of MEV-Explore, we're taking our first step at bringing transparency to MEV by quantifying the MEV that has been extracted on-chain.

[Quantifying MEV: Introducing MEV-Explore v0 (blogpost)](https://medium.com/flashbots/quantifying-mev-introducing-mev-explore-v0-5ccbee0f6d02)

## Our organization
Flashbots research and development are tightly-coupled dual engines that propel the organization in a phased approach:
- **Long-term oriented research efforts** spec out and update our roadmap, define our organization’s phases and identify key milestones associated with each of them;
- **Milestone-oriented engineering efforts** ship core infrastructure and ecosystem tools, while collecting data and producing other artifacts that feed back into research.

![FlashbotsOrg](Flashbots_org_202104.png)

### Interested in contributing? 
We are not your typical project, we are fully remote and our principles are based on that of a [pirate hacker collective](https://www.youtube.com/watch?v=T0fAznO1wA8). If you are a self-directed individual who puts collective success above your own and are motivated by solving hard problems with asymmetric impact, you will fit right in. 

* [open job positions](https://github.com/flashbots/pm/tree/main/jobs) - full-time roles we're actively recruiting for.
* [flashbots research fellowship](https://github.com/flashbots/mev-research/blob/main/research_fellowship.md) - we issue research grants to flashbots research proposals submitters. Find out more in our research repo.
* flashbots part-time contractor - become a part-time contractor in Flashbots and join one of our ongoing projects. Reach out to the team to learn more!
* nothing fits in the above? reach out at info@flashbots.net 

## Resources

### New to MEV? Here's where to get started
* [Ethereum is a Dark Forest](https://medium.com/@danrobinson/ethereum-is-a-dark-forest-ecc5f0505dff) by Dan Robinson & Georgios Konstantopoulos
* [How to get Front-Run on Ethereum mainnet](https://youtu.be/UZ-NNd6yjFM) by Scott Bigelow
* [Escaping the Dark Forest](https://samczsun.com/escaping-the-dark-forest/) by samczsun
* [Frontrunning in DEXs, Miner Extractable Value, and Consensus Instability](https://youtu.be/vR1v7AQ8i3k) by Phil Daian at IEEE Symposium on Security and Privacy
* [Flashbots: Frontrunning the MEV Crisis](https://medium.com/flashbots/frontrunning-the-mev-crisis-40629a613752) by Flashbots
* [ETHOnline - Phil & Georgios Talk Miner Extractable Value](https://youtu.be/tv0CkmcoGkM)
* [Quantifying MEV: Introducing MEV-Explore v0](https://medium.com/flashbots/quantifying-mev-introducing-mev-explore-v0-5ccbee0f6d02) by Flashbots

You can check out a full list of MEV resources in our [Research Vault](https://github.com/flashbots/mev-research#ship-vault)

### MEV Roast Recordings!

MEV Roast is a monthly discussion that stems from the research collective MEV Pi-rate Ship tradition. It's meant to service as a communication bridge between Flashbots and the broader community interested in understanding MEV and contribute to our research and engineering efforts. The format of our Roast is meant to invite constructive criticism on the Flashbots efforts and to address open questions. 
| No. |Date | Roast Master | Topic | Agenda | Notes | Recording |
|:---|:---|:---|---|---|---|:---|
8 | Nov 25 | Dan Robinson | Intro to MEV & Flashbots | [agenda](https://github.com/flashbots/pm/issues/1) | [slides](https://docs.google.com/presentation/d/1R0P-ypkiPJw-re0oKlkXkCF03UaVq_0fVnO9J0ewh1Q/edit?usp=sharing) | [video](https://drive.google.com/file/d/1bMwtTKtOITYr2DV_59RngOQADL4I3cWR/view?usp=sharing) |
9 | Dec 16 | Dan Elitzer | MEV Mitigation on Network vs. Contract Level? | [agenda](https://github.com/flashbots/pm/issues/2) | [slides](https://docs.google.com/presentation/d/1fh-kdjnR-R6qpl3NE4wb6w2OAuMGBbSykKeCzM0ga6g/edit?usp=sharing) | [video](https://drive.google.com/file/d/1V2tJB6j7DVkQ9YKTShA5KACJNAk0kZje/view?usp=sharing) |
10 | Jan 13 | Tom Schmidt / Palkeo | Whitehat Rescue of NFTs using Flashbots | [agenda](https://github.com/flashbots/pm/issues/3) | [slides](https://docs.google.com/presentation/d/1G-mzdBUnEODZnNBhy8yppF2LBImVy9-F39FFCeypoAA/edit?usp=sharing) | [video](https://drive.google.com/file/d/1s7L4vToP5v5_j5rpqES8CYzyF7mOto5Z/view?usp=sharing) |
11 | Jan 28 | Sunny Aggarwal | Privacy Solutions for MEV Minimization | [agenda](https://github.com/flashbots/pm/issues/6) | [slides](https://docs.google.com/presentation/d/1tvyDza7svyShgClosvAshg6qgs0LDIWiYwrPKGphnqQ/edit?usp=sharing) | [video](https://drive.google.com/file/d/1_4-E_i6WIDMNRDIgBIf0YiaJtm33XW9s/view?usp=sharing) |
12 | Feb 25 | Hudson Jameson | MEV State Space Search: MEV from A to Z | [agenda](https://github.com/flashbots/pm/issues/16) | [slides](https://docs.google.com/presentation/d/1I0cTl74p3MF_ZSyfjoH4A3YC6nJkK3_Ot302jiNPs5I/edit#slide=id.gbfd4392ce8_0_5) | [video](https://drive.google.com/file/d/1RLWCdvd47PHLN_az762r3G00BsnBumnb/view?usp=sharing) |
13 | Mar 25 | Vitalik Buterin | The Blockspace Bazaar: Past, Present & Future | [agenda](https://github.com/flashbots/pm/issues/44) | [slides](https://docs.google.com/presentation/d/1XxRHJrLNEEHQjQ00SISbmcuXi1063G7P_8WBEwOrmk4/edit?usp=sharing) | [video](https://zoom.us/rec/share/8jYUy6ITG_FIZ2eM_yiWG1_yYINGTgBLFVeTHE2qcUEc2CEK4O3cVNAeUSO12Pti._gUhGQx6bOI7swWs) |
14 | May 6 | Justin Drake | Scaling Ethereum | [agenda](https://github.com/flashbots/pm/issues/72) | [slides](https://docs.google.com/presentation/d/1J_MbHPznkZTQYFRGvaQhEsNACkheiw9uhRm4NwZx3kc/edit?usp=sharing) | [video](https://youtu.be/krlAqKsdLkw) |

### Flashbots Community Calls

Flashbots Community Calls is a monthly progress update from the Flashbots crew to provide transparency but also address community questions.  
| No. |Date |Topic | Agenda | Slides | Recording | Transparency Report |
|:---|:---|:---|---|---|---|:---|
1 | Mar 4 | Update & MEV-searcher Workshop | [agenda](https://github.com/flashbots/pm/issues/21) | [slides](https://docs.google.com/presentation/d/1CpEgxIPab5xo6vY64xqq2hOSvcjYSEeOY8GjVLyPkOo/edit?usp=sharing) | [recording](https://drive.google.com/file/d/1AOL5MZxPvg9eAeXr8gTPYxb_UH92TZZC/view?usp=sharing) | [Februrary Transparency Report](https://medium.com/flashbots/flashbots-transparency-report-february-2021-8ac45b467d0a) |
2 | Apr 15 | Update & Flashbots Dashboard Preview | [agenda](https://github.com/flashbots/pm/issues/67) | [slides](https://docs.google.com/presentation/d/1224x7eaL9MCP93af8X3qGJ35QuBqNT-OJeGpE10plyQ/edit?usp=sharing) | [recording](https://zoom.us/rec/share/wZ3CnhN-vVoo35J4GAvwrn9xOxg17XsHHu0c2KH9ZUHJHQEvPFN82yaqInf8-q2H.My3Z1vP-rfR_y1Qr) | [March Transparency Report](https://medium.com/flashbots/flashbots-transparency-report-march-2021-d3930b4b98a9) |


### Other Flashbots resources
* [Flashbots: Frontrunning the MEV crisis (Eth Research post)](https://ethresear.ch/t/flashbots-frontrunning-the-mev-crisis/8251)
* [explore.flashbots.net](https://explore.flashbots.net)
* [Flashbots Discord](https://discord.gg/7hvTycdNcK)

### Navigate Flashbots' Github
[/pm](https://github.com/flashbots/pm)
<br> The repo you're currently in, contains an introduction to our organization, our job postings and the list of all the projects we're working on, updated frequently by the project's owners.

[/mev-geth](https://github.com/flashbots/mev-geth)
<br> A fork of the Geth client dubbed MEV-Geth modified to accept Flashbots transaction bundles and to compare Flashbots blocks to regular blocks.

[/mev-geth-demo](https://github.com/flashbots/mev-geth-demo)
<br> Launches an MEV GETH node, and shows how a miner may profit from it by accepting MEV bundles

[/mev-proxy](https://github.com/flashbots/mev-proxy)
<br> An example of a reverse proxy that a miner can run.

[/mev-relay-js](https://github.com/flashbots/mev-relay-js)
<br> The code for the entrypoint searchers send their Flashbots bundles to and miners connect to in order to receive bundles. Includes an example of a reverse proxy that a miner can run.

[/ethers-provider-flashbots-bundle](https://github.com/flashbots/ethers-provider-flashbots-bundle)
<br> Flashbots provider for ethers.js

[/web3-flashbots](https://github.com/flashbots/web3-flashbots)
<br> Flashbots provider for web3.py 

[/simple-arbitrage](https://github.com/flashbots/simple-arbitrage)
<br> An example arbitrage bot that uses Flashbots.

[/mev-inspect-rs](https://github.com/flashbots/mev-inspect-rs)
<br> The Rust codebase of the inspectors we run to scrape Ethereum data we use in MEV-Explore.

[/mev-explore-public](https://github.com/flashbots/mev-explore-public)
<br> All things MEV-Explore: report bugs, request features by opening an issue and start a discussion in the discussion tab.

[/mev-research](https://github.com/flashbots/mev-research)
<br> All our research activity. Learn about our research organization, the MEV research fellowship, submit a Flashbots research proposal as a PR, open an issue to start a discussion.

