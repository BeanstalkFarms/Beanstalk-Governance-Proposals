# BFP-78: Set Initial Minting Schedule at Replant

Proposed: June 26, 2022

Status: Passed

Link: [Snapshot](https://snapshot.org/#/beanstalkfarms.eth/proposal/0x4289cde88a6c3c0be830b0b8f39bba3f54258a77947bec0f37b0668e12e530ea)

---

- [Proposer](#proposer)
- [Summary](#summary)
- [Proposal](#proposal)

## Proposer

Beanstalk Farms

## Summary

* Mint 1% of deltaB at the end of the first full Season after Replant; increase minting percent of deltaB by 1% each Season until 100% is reached.

## Proposal

It is unclear how the market will value Bean initially, and the primary reason that the Bean price was as high as it was pre-exploit was because generalized minting wasn’t yet live. A ramped minting schedule allows time for Bean to price itself post-exploit.

Beanstalk Farms proposes Replanting Beanstalk with a ramp-up period for deltaB (R) as a % of deltaB that will be minted, such that deltaB is: 

deltaB = deltaB * R

Where R starts at 0% and increases by 1% at the start of each Season for 100 Seasons. This ramp-up period affects both Bean and Soil minting. While this minting schedule is conservative, the ramp-up is also quick. The regular minting schedule of 100% will resume about 4 days after Replant.

**Code with this minting schedule is currently being audited. Changing the minting schedule would technically be an unaudited change, but would only require changing one or more constants, depending on the change to the minting schedule.**
