# BFP-67: Barn Raise Proposal

Proposed: April 25, 2022

Status: Passed

Link: [Snapshot](https://snapshot.org/#/beanstalkfarms.eth/proposal/0x54fad9c756daa38bb4bafadbee2cea6cb98f380fe2d6a62fdf723d0b15430d42)

---

- [Proposer](#proposer)
- [Summary](#summary)
- [Problem](#problem)
- [Proposed Solution](#proposed-solution)
- [Rationale](#rationale)

## Proposer

Beanstalk Farms

## Summary

- Beanstalk will host the Barn Raise, a public ten-day fundraiser to restore up to ~$77M in non-Bean liquidity stolen from the Silo.
- Pods Beanstalk issues during the Barn Raise will become Harvestable on a FIFO basis based on a new Barn Raise Pod Line (BR Pod Line).
- One-third of future Bean mints will go towards the BR Pod Line, one-third to Stalk holders, and one-third to the existing Pod Line. When the entire BR Pod Line has Harvested, the normal minting schedule will resume.
- The state of pre-exploit Beanstalk assets will be scaled as a function of the percent of the Barn Raiser that is filled. This applies to Beans, Stalk, Seeds, BDV in the Silo and Pods.
- Any assets that are recapitalized (Deposited Beans, Deposited LP Tokens and circulating Beans) during the Barn Raise will be subject to a vesting schedule based on the percent of the BR Pod Line that has Harvested.
- The attacker’s balances prior to the exploit will be removed.

## Problem

**Governance Exploit**

From Day 1, Beanstalk has been owned solely by members of the Beanstalk DAO via a decentralized, on-chain governance structure. Unfortunately, a bad actor exploited that very structure on April 17 to steal all Silo assets, including ~$77M in non-Bean liquidity. This was an attack on Beanstalk's governance model, not its economic design.

## Proposed Solution

**The Barn Raise**

Beanstalk Farms proposes having Beanstalk host the Barn Raise – a public ten-day fundraiser to restore up to ~$77M in non-Bean liquidity stolen from the Silo – beginning on May 2. The Barn Raise will consist of two phases: (1) a seven-day Bidding Period, followed by (2) a three-day Sowing Period. Any Ethereum wallet will be able to lend to Beanstalk during the Barn Raise via the BR Pod Line. There will be ~77M Soil available during the Barn Raise, meaning that a maximum of ~$77M can be Sown before the Barn Raise ends.

**The Bidding Period**

Between 4:00pm UTC on 5/2 and 3:59pm UTC on 5/9, participants will have the option to submit Bids for Pods — Beanstalk's native debt asset — using two parameters:

The Weather (interest rate) for Sowing (lending to Beanstalk); and
The size of the Bid, denominated in another USD-pegged stablecoin token.
When Bidding, the assets to be Sown are locked in at the time of submission. Bids will not be cancellable once submitted, but may be updated to lower the Weather.

Bids placed earlier in the Bidding Period will receive a bonus. The bonus for submitting a Bid will start at 21% on Day 1 of the Bidding Period and decrease by 3% per day until the end of the Bidding Period. The bonus will decrease at 4:00pm UTC. Updating a Bid resets the bonus. The bonus will be added to the Weather. For example, a $100 Bid for 120% with a 21% bonus would yield 100 * (100 + 120 + 21) = 241 Pods.

During the Sowing period, Bids will be auto-filled when the Bid's Weather is reached, given there is Soil available. Bids will be partially fillable.

Bids at the same Weather are Sown in the order in which they were received, meaning earlier Bids at a given Weather have priority in the BR Pod Line. Bids placed at or below 20% Weather will automatically be Sown when the Sowing Period begins, and be ordered by Weather first and time second.

**The Sowing Period**

At 4:00pm UTC on 5/9, the Weather (interest rate) will start at 20% and increase by 1% every 10 minutes for the duration of the Sowing Period, which will end after three days or when the Soil reaches 0 -- whichever occurs first.

Each time the Weather increases, the Bids at the new Weather will be auto-filled and placed in the BR Pod Line, so long as there is available Soil. Bids that are not successfully filled during the Sowing Period will be claimable after Beanstalk is Unpaused.

Participants can Sow at any time during the Sowing Period, so long as there is available Soil. Sows will clear based on the Weather at the time of the Sow.

**Beanstalk Reseeding**

After the Sowing Period concludes, there will be a brief interlude prior to Unpausing Beanstalk, during which core contributors and the community will ensure that satisfactory governance and safety measures are in place.

The timeline for Beanstalk to be Unpaused will be determined by a subsequent Snapshot vote. Given that the results of the Barn Raise would be expected to meaningfully influence the vote, the Snapshot will be proposed after the completion of the Barn Raise.

Once Beanstalk is Unpaused, one-third of future Bean mints will go towards the BR Pod Line, one-third to Stalk holders, and one-third to the existing Pod Line. The BR Pod Line, like the existing Pod Line, will be repaid on a FIFO basis. After the BR Pod Line completely Harvests, the new Bean mint schedule will revert to Beanstalk's original one-half Silo and one-half existing Pod Line allocation.

Capital Sown during the Barn Raise will be used to recapitalize stolen funds pro rata to the Silo. The protocol will scale its obligations based on the amount of capital raised. This means that if X% of the ~$77M is raised, participants will receive X% of their pre-exploit Beans, Stalk, Seeds, BDV in Silo, and Pods. Beanstalk will Unpause with the pre-exploit Weather.

Silo Members and Bean holders’ recapitalized assets (Beans, Stalk, Seeds, BDV in Silo) will be placed under a vesting schedule based on the percentage of the BR Pod Line that has become Harvestable. If these users choose to Claim their assets before the BR Pod Line has been fully repaid, they will face a haircut proportional to the amount of the BR Pod Line repaid. For example, if a Silo Member decides to Claim their recapitalized assets and 50% of the BR Pod Line has been repaid, they will forfeit their rights to 50% of those assets. Farmable Beans earned from new mints after Unpause are not subject to this vesting schedule.

While raising the full ~$77M is a sufficient – and ideal – condition of a successful Barn Raise, it is not a necessary one. Because the Reseeding is designed to scale the protocol based on the size of the Barn Raise, Beanstalk will be able to Unpause with an arbitrary amount of new liquidity. In practice, raising $0 is the equivalent of restarting Beanstalk from scratch. Therefore, any amount raised is better than nothing.

## Rationale

Unlike every other non-collateralized stablecoin in history, Beanstalk’s economic model has not failed. Beanstalk, however, cannot continue in its state prior to the devastating governance attack on April 17.

Beanstalk Farms and the broader Beanstalk community are keen to bring Beanstalk’s key stablecoin innovations to every corner of DeFi, in spite of last week’s events. A successful Barn Raise is the best path to achieving that.
