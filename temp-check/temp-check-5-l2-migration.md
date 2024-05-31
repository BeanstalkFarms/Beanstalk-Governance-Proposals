# Temp-Check-5: L2 Migration

Proposed: May 21, 2024

Status:

* For: 52,740,001 Stalk, 93.78%
* Against: 3,496,827 Stalk, 6.22%

Link: [Snapshot](https://snapshot.org/#/beanstalkfarms.eth/proposal/0x93dfc538a66c1c199f5c9f0fd9c0233ce3625c7ada9743bafc8b5fbc0fc38fc7)

---

- [Proposer](#proposer)
- [Summary](#summary)
- [Process Note](#process-note)
- [Problem](#problem)
- [Proposed Solution](#proposed-solution)
- [Voting Choices](#voting-choices)

## Proposer

Brean

## Summary

Vote to signal support For or Against any potential form of migrating Beanstalk to an Ethereum L2.

This proposal is **not** to signal support For or Against **any particular L2**—it's to better understand the DAO's appetite for migrating to an L2 in the first place.

## Process Note

This temperature check proposal is intended for the DAO to express a preference in advance of a future BIP. There is no quorum for this proposal given that the goal is to simply gather information and feedback. Stalkholders are still strongly encouraged to participate in order for the outcome of the vote to be as representative as possible. The vote will last 7 days.

## Problem

Beanstalk is a fairly gaseous protocol. While the costs of interacting with Beanstalk at the time of writing in May 2024 are reasonable, activity on Ethereum L1 is currently low. In the past when Gwei has reached mid to high double digit values, interacting with Beanstalk (such as Mowing, Planting, or Converting) have cost upwards of several hundred US dollars. This prices out smaller Farmers and reduces the efficacy of Beanstalk's peg maintenance mechanisms. 

While Ethereum L1 remains one of the most trustless and censorship resistant networks to deploy smart contracts on, the roadmaps for various Ethereum L2s are becoming more fully featured and focused on decentralization (including decentralization of fraud proof submitters, the permissionless ability to exit the L2 at any time, etc.).

Through governance, Beanstalk has gone through many upgrades. These series of upgrades has generally increased the average gas per interaction in Beanstalk (Silo V3 requires a user to Mow on a per token basis, getting manipulation resistant data costs more than non manipulation resistant data, adding new Wells to the Deposit/Minting Whitelist increases the cost to call `gm`). While trustlessness and censorship resistance are axes that the Beanstalk DAO should evaluate when choosing a chain, the cost per interaction is a metric that has historically been put on the back burner.

Even when the Beanstalk development community more heavily prioritized lowering gas costs (which has now shifted in favor of [prioritizing security](https://github.com/BeanstalkFarms/Beanstalk/issues/729)), most of the cost of interacting with Beanstalk on Ethereum L1 was (and continues to be) unavoidable. If there's interest from the DAO in pursuing migrating Beanstalk to an L2, it's best to do it proactively before gas costs become prohibitive for everyday participants.

The high cost of development on L1 is often prohibitive in the implementation of Beanstalk. Many of the ideas proposed in the Beanstalk community would require significant changes in the Beanstalk state. The Beanstalk community should be able to freely discuss ideas without the need to consider the execution cost so significantly.

## Proposed Solution

Migrate the Beanstalk peg mechanism and Beanstalk state to an Ethereum L2. [L2BEAT](https://l2beat.com/scaling/summary) is a great resource to see the different stages of development and decentralization that various Ethereum L2s are in.

If Beans are simply bridged to L2s, access the Silo, Field, and Barn would still take place on L1 and peg maintenance would be unaffected. Thus, migrating the system entirely is preferred.

At a high level, the process would look something like the following (upon a L2 migration BIP passing):
* Pause L1 Beanstalk;
* Transfer Deposited LP tokens to the L1 BCM;
* Withdraw liquidity and send the non-Bean assets to the L2 BCM;
* Deploy a new Beanstalk on the desired L2 in a state that does not yet allow any interaction;
    * Includes deploying all necessary facets, initializing the Whitelisted assets, deploying Basin/Pipeline/Depot, etc.;
* Execute a series of transactions from the L2 BCM that copy the L1 Beanstalk state onto L2 Beanstalk;
* Seed liquidity from the L2 BCM and send the LP tokens to L2 Beanstalk; and
* Unpause L2 Beanstalk.

There would need to be more decisions made by the DAO to determine things like:
* How to handle the 3CRV from the deposited BEAN3CRV (some L2s don't have 3CRV);
* How to handle Farm Balances (particularly tokens that don't have an L2 equivalent);
* How to handle migrating smart contract balances (given there's no guarantee of owning the same address on L2); and
* Additional Wells to whitelist at the time of L2 migration (and initial parameters for each Well).

## Voting Choices

Voting For is to express interest in exploring some form of migrating Beanstalk peg mechanism and state to an Ethereum L2. Voting Against is to express interest against doing so.

* For
* Against
