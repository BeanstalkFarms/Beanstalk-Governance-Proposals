# BFP-75: Choose BEAN:3CRV for the Replant Liquidity Pool; Set Initial Minting Schedule

Proposed: June 10, 2022

Status: Failed

Link: [Snapshot](https://snapshot.org/#/beanstalkfarms.eth/proposal/0xa3834c289604364a1cb0cddfc6397f89bb66fac673ca82e7869fee7167e92431), [Arweave](https://arweave.net/KXn6qIqe2-bNC848nOI3wMY0-oGHISQCvyVW8FOo0eM)

---

- [Proposer](#proposer)
- [Summary](#summary)
- [Proposal](#proposal)

## Proposer

Beanstalk Farms

## Summary

Replant Beanstalk with a single liquidity pool for Beans—BEAN:3CRV, a metapool on Curve with an A parameter of 10.

## Proposal

Before the exploit on April 17, 2022, Beans were trading in 3 liquidity pools—BEAN:ETH on Uniswap V2, BEAN:3CRV on Curve (A of 10), and BEAN:LUSD on Curve (A of 100).

For the sake of technical simplicity, Beanstalk Farms recommends Replanting Beanstalk with a single Curve BEAN:3CRV stableswap liquidity pool. Curve stableswap pools also have significantly better concentrated liquidity over constant product pools. Pairing with 3CRV has minimal centralization risk as USDC or USDT would have to blacklist the 3CRV pool, which is most of the Curve ecosystem. 

Curve metapool-specific code is currently being audited. Choosing another starter pool could be done, but it would likely push back the Replant a couple of weeks. Beanstalk Farms intends to launch and propose a BIP to whitelist a new BEAN:ETH pool shortly after Replant.

What constitutes a healthy distribution of liquidity against different assets is something the community should always be considering.

**Additional Notes**

* Liquidity that is recapitalized by the Barn Raise will be added to the pool at the ratio that would cause the deltaB to trend towards the pre-exploit deltaB in the BEAN:3CRV pool.
* Given the high deltaB in the BEAN:3CRV pool pre-exploit, generalizing minting was going to be launched with a ramp-up period. Beanstalk will be Replanted with a ramp-up period for deltaB—a % of deltaB will be minted, starting at 1% and increasing 1% every Season until 100% is reached. This ramp-up period affects both Bean and Soil minting.
* BFP-75 is meant to signal support for Replanting Beanstalk with the BEAN:3CRV pool. The BEAN:3CRV LP Token will be whitelisted for the Silo in the BIP that Replants Beanstalk.
