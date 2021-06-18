# Flashbots

Flashbots is a research and development organization working on mitigating the negative externalities of current MEV extraction techniques and avoiding the existential risks MEV could cause to state-rich blockchains like Ethereum. Our primary focus is to enable a permissionless, transparent, and fair ecosystem for MEV extraction. It falls under three goals: Bringing Transparency to MEV Activity, Democratizing Access to MEV Revenue and Enabling Fair Redistribution of MEV Revenue

Our efforts are focused on three verticals:
* Flashbots Auction: a private communication channel between miners and searchers for transparent and efficient MEV extraction.
* Flashbots Data: a suite of tools for increasing MEV transparency and reducing information asymmetry.
* Flashbots Research: an open, transparent, and collaborative research effort to tackle short and long term research questions relevant to MEV.

The Flashbots organization is funded by long term capital partners with a track record of alignment with the ecosystem. Our current capital partner is [Paradigm](https://paradigm.xyz/).

You can interact with Flashbots on Discord and Github. We have dedicated channels on Discord for each of our efforts and welcome contributions. Our work is open source and you can follow our progress in each repository of the Flashbots organization. In addition, we hold several recurring community calls: a monthly MEV Roast, a bi-weekly research workshop and a monthly transparency call to update the community on our progress. 

The current repository you're in provides an overview of our activities, and contains our [open job searches](https://github.com/flashbots/pm/tree/main/jobs) and the [onboarding documents](https://github.com/flashbots/pm/tree/main/guides) to start using Flashbots, either as a miner or as a searcher.

Learn more about our values and our mission: [Flashbots - Frontrunning the MEV Crisis](https://medium.com/flashbots/frontrunning-the-mev-crisis-40629a613752)
<br> Join us on Discord: [#Flashbots](https://discord.gg/7hvTycdNcK)
<br> Subscribe to our public calendar [here](https://bit.ly/3uJAHpo)
<br> Read our [monthly transparency reports](https://medium.com/flashbots/tagged/transparency-report)
<br> Read our [documentation](https://docs.flashbots.net)

### Index:
* [Introduction](https://github.com/flashbots/pm#flashbots)
* [Flashbots Auction, Data & Research](https://github.com/flashbots/pm#flashbots-auction)
* [Our organization & ways you can contribute](https://github.com/flashbots/pm#our-organization)
* [List of resources, MEV Roasts recordings and how to navigate Flashbots' github](https://github.com/flashbots/pm#resources)


----
## Flashbots Auction

Flashbots Auction is a permissionless, transparent, and fair ecosystem for efficient MEV extraction and frontrunning protection which preserves the ideals of Ethereum. Flashbots Auction provides a private communication channel between Ethereum users and miners for efficiently communicating preferred transaction order within a block. Flashbots Auction consists of mev-geth, a patch on top of the go-ethereum client, along with the mev-relay, a transaction bundle relayer.

Learn more about Flashbots Auction in our [documentation](https://docs.flashbots.net/flashbots-core/overview), and start using it as a [miner](https://docs.flashbots.net/flashbots-core/miners/quick-start) or as a [searcher](https://docs.flashbots.net/flashbots-core/searchers/quick-start)!

We are quickly iterating in public towards a permissionless infrastructure with full transaction privacy. We welcome all ecosystem participants to provide early technical feedback via Github issues and pull requests in the relevant [repos](https://github.com/flashbots), create open-source toolings to improve transparency within Flashbots ecosystem, and participate in ongoing discussions on Flashbots' [Discord](https://discord.gg/3TjWjBerRb)!


## Flashbots Data

Flashbots Data currently consists of [MEV-Explore v0](https://explore.flashbots.net) a public dashboard and live transaction explorer for MEV transactions, and the [Flashbots dashboard](https://dashboard.flashbots.net) a public dashboard of the activity taking place on Flashbots.

MEV-Explore crawls the Ethereum blockchain and classifies MEV transactions over 8 major DeFi protocols and their clones. The code that feeds data into it is open-source and accessible through [mev-inspect](https://github.com/flashbots/mev-inspect-rs).

The Flashbots Dashboard is a collection of real time metrics on the Flashbots network that goes back to the first pre-release test bundle mined on-chain on December 29, 2021.

## Flashbots Research

We are building Flashbots Research as a research hub for MEV, tackling and uncovering questions relevant to the short, medium and long-term future of the ecosystem. We view this research as essential to help the cryptocurrency ecosystem better understand and deal with MEV. We are currently funded through venture capital but aim to build a sustainable self-funded independent research organization that can become a place where ambitious researchers can work amongst like-minded individuals on the most interesting research questions unencumbered from the usual friction of academia.

Some of the research we've released includes: [an early exploration of MEV in eth2](https://hackmd.io/@flashbots/mev-in-eth2), a framework to [quantify Realized Extractable Value](https://hackmd.io/@flashbots/quantifying-REV) and a proposal for [a sealed bid MEV auction design using secure enclaves](https://ethresear.ch/t/mev-sgx-a-sealed-bid-mev-auction-design/9677).

Learn more about our research efforts and how you can contribute to them on our [Flashbots Research repository](https://github.com/flashbots/mev-research).

## Our organization
Flashbots research and development are tightly-coupled dual engines that propel the organization in a phased approach:
- **Long-term oriented research efforts** spec out and update our roadmap, define our organization’s phases and identify key milestones associated with each of them;
- **Milestone-oriented engineering efforts** ship core infrastructure and ecosystem tools, while collecting data and producing other artifacts that feed back into research.

![FlashbotsOrg](Flashbots_org_202104.png)

### Interested in contributing? 
We are not your typical project, we are fully remote and our principles are based on that of a [pirate hacker collective](https://www.youtube.com/watch?v=T0fAznO1wA8). If you are a self-directed individual who puts collective success above your own and are motivated by solving hard problems with asymmetric impact, you will fit right in. 

* [open job positions](https://github.com/flashbots/pm/tree/main/jobs) - full-time roles we're actively recruiting for.
* [MEV research fellowship](https://github.com/flashbots/mev-research/blob/main/grants.md) - we issue research grants to flashbots research proposals submitters. 
* flashbots part-time contractor - become a part-time contractor in Flashbots and join one of our ongoing projects. Reach out to the team to learn more!
* nothing fits in the above? reach out at jobs@flashbots.net 

## Resources

### New to MEV? Here's where to get started
* [Ethereum is a Dark Forest](https://medium.com/@danrobinson/ethereum-is-a-dark-forest-ecc5f0505dff) by Dan Robinson & Georgios Konstantopoulos
* [How to get Front-Run on Ethereum mainnet](https://youtu.be/UZ-NNd6yjFM) by Scott Bigelow
* [Escaping the Dark Forest](https://samczsun.com/escaping-the-dark-forest/) by samczsun
* [Frontrunning in DEXs, Miner Extractable Value, and Consensus Instability](https://youtu.be/vR1v7AQ8i3k) by Phil Daian at IEEE Symposium on Security and Privacy
* [Flashbots: Frontrunning the MEV Crisis](https://medium.com/flashbots/frontrunning-the-mev-crisis-40629a613752) by Flashbots
* [ETHOnline - Phil & Georgios Talk Miner Extractable Value](https://youtu.be/tv0CkmcoGkM)
* [Quantifying MEV: Introducing MEV-Explore v0](https://medium.com/flashbots/quantifying-mev-introducing-mev-explore-v0-5ccbee0f6d02) by Flashbots

You can check out a full list of MEV resources in our [Research Vault](https://github.com/flashbots/mev-research/blob/main/resources.md).

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
3 | May 13 | Update on v0.2, Flashbots Research & release of Flashbots Dashboard | [agenda](https://github.com/flashbots/pm/issues/73#issue-891158240) | [slides](https://docs.google.com/presentation/d/1hppwu2gUUY80n4mNjjb49GNwITRkkPheuWwgIjvr-EI/edit?usp=sharing) | [recording](https://zoom.us/rec/share/x4jSYPQAoSHH-cBuhxpORNnXAPpdT8ovCLTtlWgcSCR2hLpiFsKtYrjfnMFpTeq9.q6-p05D7zdiHeUdB) | [April Transparency Report](https://medium.com/flashbots/flashbots-transparency-report-april-2021-9fef4d8dde07) |

### Other Flashbots resources
* [Flashbots: Frontrunning the MEV crisis (Eth Research post)](https://ethresear.ch/t/flashbots-frontrunning-the-mev-crisis/8251)
* [Quantifying MEV: Introducing MEV-Explore v0](https://medium.com/flashbots/quantifying-mev-introducing-mev-explore-v0-5ccbee0f6d02)
* [Epicenter podcast](https://youtu.be/YfNeKo8FVjc), [ZK Podcast](https://youtu.be/p5L39ab7n4k), [Bankless podcast](https://youtu.be/rOVz7dOrGyY)

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

