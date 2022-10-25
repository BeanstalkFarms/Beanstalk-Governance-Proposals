# BFP-76: Choose Replant Liquidity Pool

Proposed: June 17, 2022

Status: Passed

Link: [Snapshot](https://snapshot.org/#/beanstalkfarms.eth/proposal/0xae6e909e82ee6c0ffd0ae266dd9b6e2d07894af62c6ba8de76ef0002489eb2a8), [Arweave](https://arweave.net/f4g3TSdDm8lpLTKXHKcP2oeDA0zs0oJ_LVz28wW6GK8)

---

- [Proposer](#proposer)
- [Summary](#summary)
- [Proposal](#proposal)
- [Voting](#voting)

## Proposer

Beanstalk Farms

## Summary

Vote on the liquidity pool to Replant Beanstalk with. 

## Proposal

Before the exploit on April 17, 2022, Beans were trading in 3 liquidity pools—BEAN:ETH on Uniswap V2, BEAN:3CRV on Curve (A of 10), and BEAN:LUSD on Curve (A of 100).

For the sake of technical simplicity, Beanstalk Farms recommends Replanting Beanstalk with a single liquidity pool. Liquidity that is recapitalized by the Barn Raise will be added to this pool at the ratio that would cause the deltaB to trend towards the pre-exploit deltaB in the Replant pool.

Curve metapool-specific code is currently being audited. Choosing another starter pool could be done, but it would push back the Replant by at least a month. BIPs to whitelist other trading pairs with liquid, censorship-resistant assets can be proposed shortly after Replant.

What constitutes a healthy distribution of liquidity against different assets is something the community should always be considering.

## Voting

Which asset should Beans trade against in the single liquidity pool at Replant? The other liquidity pool can be added shortly after Replant.

* 3CRV; Replant on time
* ETH; Replant a month later
