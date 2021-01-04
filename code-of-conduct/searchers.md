# Searcher Code of Conduct

Flashbots allows searchers to communicate with the mev-relay service, which processes and forwards bundles directly to a set of trusted miners. Given that failed searcher transactions do not land on the chain, resulting in funds spent on transaction fees, this mev-relay is evaluating Ethereum transactions with nothing at stake from the sender.

Due to this imbalance between the resources required for bundle submission to mev-relay and the resources consumed by mev-relay, it is important to establish an expectation of behavior from participating searchers. Violating this code of conduct will result in rate-limiting or suspension of a searcher's API key.

These measures are necessary for the public beta but may be changed in the future.

- **Rate-limiting**: each API key will have an enforced rate-limit. The searcher will adhere to this limit and implement back-offs when receiving an "HTTP 429 Too Many Requests"
- **Keep gas limits low**: For bot-signed transactions, keep gas limits as low as possible. Avoiding excessively large gas limits will help mev-relay estimate the upper-bound of evm processing requirements
- **Submit bundles with relevant transactions**: While a bundle could theoretically contain a block's worth of transactions, this is not necessary as miners will order standard pending transactions behind your bundle for maximum profitability

Some important notes:
- `relay.flashbots.net` retains detailed logs of all bundles including the IP address of the sender and the content of the bundles
- `relay.flashbots.net` maintains a whitelist of trusted miners and we will remove miners if they are found to misbehave
- `relay.flashbots.net` maintains a whitelist of searchers and we will remove searchers if they are found to misbehave

## Application Form

To sign up and receive your API keys, please fill [this form](https://docs.google.com/forms/d/e/1FAIpQLSd4AKrS-vcfW1X-dQvkFY73HysoKfkhcd-31Tj8frDAU6D6aQ/viewform).
