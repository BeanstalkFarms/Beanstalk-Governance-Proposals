# BOP-2: Increase and Revisit Bounty for April Governance Exploit

Proposed: December 10, 2022

Status: Passed

Link: [Snapshot](https://snapshot.org/#/beanstalkdao.eth/proposal/0xab93c3857af998c0bf70049fc82cf191407a52d9a710ce7ad1a6ddb7a7b3601c), [Arweave](https://arweave.net/aOAzXi2IzO5Ts1OrXYVrAjXbBavbKg07k7k56gIXtl4)

---

- [Proposer](#proposer)
- [Summary](#summary)
- [Quorum](#quorum)
- [Problem](#problem)
- [Proposed Solution](#proposed-solution)
- [Technical Rationale](#technical-rationale)
- [Economic Rationale](#economic-rationale)
- [Proposer Reward](#proposer-reward)
- [Distribution of Rewards](#distribution-of-rewards)
- [Beans Minted](#beans-minted)
- [Effective](#effective)

## Proposer

Sync, in collaboration with [Hats Finance](https://hats.finance/), led the ideation and compilation of this Beanstalk Operations Proposal (BOP). Following its extensive deliberation within the Beanstalk community, it is being proposed for the Beanstalk DAO’s consideration, to affirm its collective agreement to attempt to achieve a resolution of the April exploit if possible. 

**Proposer Wallet**: [0x9c52dc78bd84007bf63987806f0aeeece0ef14a6](https://etherscan.io/verifySig/12445)

## Summary

Increase the bounty for Beanstalk's April exploit from 10% to 40% and revisit it formally via the use of an "Ethical Return" smart contract that could facilitate a resolution of the incident. See the “Proposed Solution” section below.

In accordance to [BFP-81](https://snapshot.org/#/beanstalkfarms.eth/proposal/0xa24c368f08093b8a5e27c0b3ae9296eb60272cddc8882434b02a86152d903e59), this proposal is classified as a BOP. As noted, the purpose of a BOP is for the “DAO voting on things other than protocol changes." Since this proposal does not involve a protocol code change to Beanstalk itself, it is thus considered a BOP.

## Quorum

Quorum for a BOP is >35% of the Stalk supply participating in the vote and voting “For.”

## Problem

Beanstalk suffered a governance exploit on April 17, 2022 that netted the exploiter about $77M in non-Beanstalk assets (roughly the value of the 24,830 ETH taken at the time):[ https://etherscan.io/tx/0xcd314668aaa9bbfebaf1a0bd2b6553d01dd58899c508d4729fa7311dc5d33ad7](https://etherscan.io/tx/0xcd314668aaa9bbfebaf1a0bd2b6553d01dd58899c508d4729fa7311dc5d33ad7) 

Beanstalk Farms offered the exploiter 10% as a bounty after the incident. However, the exploiter did not engage the Beanstalk Farms team on the offer:[ https://twitter.com/BeanstalkFarms/status/1516156014264938501](https://twitter.com/BeanstalkFarms/status/1516156014264938501) 

Beanstalk Farms then offered a bounty to any group that would be able to recover the exploited funds. The offered bounty was 10% of the recovered funds:[ https://twitter.com/BeanstalkFarms/status/1518624220564975616](https://twitter.com/BeanstalkFarms/status/1518624220564975616) 

Per community updates from the Beanstalk Farms team, the current information they have from analytics firms is the stolen ETH still remains in Tornado Cash. 

Recently, Mango Markets was exploited for $114M. However, the exploiter engaged their DAO and ultimately returned $67M, keeping $47M for himself and his team (about 40%). See:[ https://www.coindesk.com/business/2022/10/15/114m-mango-markets-exploiter-outs-himself-returns-most-of-the-money/](https://www.coindesk.com/business/2022/10/15/114m-mango-markets-exploiter-outs-himself-returns-most-of-the-money/) 

Considering the closure of the recent Mango Markets exploit, Beanstalk should revisit the April incident and attempt to recover the exploited ETH by offering the exploiter a higher bounty.

## Proposed Solution

Increase the offered bounty to 40%, akin to Mango Markets, such that Beanstalk would retain 60% of the returned ETH/funds as its recovered amount (58.2% net after deducting 3% reward - see “Proposer Reward” section below). 

Since the exploited ETH appears to still be in Tornado Cash, there is a chance the exploiter may resurface if a higher offer is made.

The proposed solution involves the kick off of the work stream described below:

The transfer of ETH can be facilitated via an Ethical Return smart contract developed by the Hats Finance team. Beanstalk Farms can deploy it after it is audited by Halborn and convey it via public communication channels to the hacker (via his wallet, on Twitter, etc.) 

If the exploiter is not cooperative or responsive, white hats in the Hats Finance network and beyond will also be incentivized to dig into the data surrounding the April exploit and find weak links, in hopes of facilitating the return of the funds to the Ethical Return contract themselves. In such a scenario, a white hat hacker would receive the 40% bounty instead.

## Technical Rationale

Hats Finance has supported Temple DAO with their attempt to resolve the recent $2.5M exploit of Stax Finance, whereby they facilitated a bounty offer to the exploiter via an [Ethical Return](https://github.com/hats-finance/ethical-return) smart contract:[ https://twitter.com/HatsFinance/status/1580974400182005760](https://twitter.com/HatsFinance/status/1580974400182005760) 

Hats Finance will provide guidance to Beanstalk Farms on the contract it offered to Temple DAO. Hats Finance will customize the contract to include a tipping mechanism into it, whereby their reward will be sent to the Hats Finance wallet specified as the tipping address therein, only upon a recovery of funds by Beanstalk (see “Proposer Reward” section below). The Beanstalk Farms team can subsequently have the contract audited by Halborn via its ongoing retainer agreement with them before deploying it. Beanstalk Farms will specify its beneficiary address for any recovered funds for Beanstalk, which will be coded into the Ethical Return contract. Hats Finance will be available to assist and coordinate with Beanstalk Farms on this, as well.

The Ethical Return contract has previously been audited by the Hats Finance security team, who specialize as auditors themselves. Ahead of its audit by Halborn, Beanstalk Farms can also deploy the contract to testnet and test it with a beneficiary address they choose to define therein.

In deploying the Ethical Return contract, an understanding is agreed to in advance, whereby the Beanstalk DAO will withhold all claims against the exploiter if the total amount of funds returned to the contract will be equal to or higher than 99% of the stolen funds from the April exploit (at least 24,580 ETH). Alternatively, if a white hat hacker is able to retrieve the stolen funds and return the funds to the contract, they would receive the 40% bounty instead of the exploiter.

## Economic Rationale

While there is no guarantee the higher bounty offer to the exploiter will work, this proposal offers a possible closure for them, and to thus recoup at least something for the Beanstalk DAO as such.

Please note that how any recovered ETH/funds would be handled by Beanstalk Farms on behalf of Beanstalk users is beyond the scope of this BOP and would have to be decided on separately by the Beanstalk community in a subsequent governance proposal to the Beanstalk DAO. 

It is recommended that Beanstalk Farms consult with legal experts ahead of any recovery of funds from the April exploit, to assess its options on how to best handle such a recovery for its users, in consideration of OFAC’s specific licensing request [guidance](https://home.treasury.gov/policy-issues/financial-sanctions/faqs/added/2022-09-13) for “transactions involving Tornado Cash that were initiated prior to its designation on August 8, 2022 but not completed by the date of designation.”

In general, by voting for this BOP, Beanstalk DAO members accept upfront that upon the recovery of any funds, the assets underlying [Unripe Assets](https://docs.bean.money/almanac/farm/barn#unripe-assets) will be recapitalized, since Unripe Assets represent liquidity taken from Silo Members in the April governance exploit. The manner in which a recapitalization would be facilitated as such may vary depending on when a recovery is achieved, the state of Beanstalk Protocol and overall market conditions at that time, and associated technical considerations that the Beanstalk Farms team may then need to make. 

To ensure Beanstalk Farms is afforded sufficient time to evaluate its options after a recovery scenario, it will have up to fourteen days to propose a draft governance proposal on how the recapitalization may proceed. The draft governance proposal will then be shared by Beanstalk Farms for comments with the Beanstalk community in its Discord ahead of a formal vote by the DAO. Additionally, ahead of such a hypothetical recovery scenario, the Beanstalk community is welcome to share its input on this topic in the dedicated discussion channel already established for BOP-2.

## Proposer Reward

The reward will correspond to 3% of Beanstalk’s recovered ETH/funds, to be split equally between Hats Finance, Sync, and Beanstalk Farms in the event of a recovery.

Separately, to cover the cost of an audit of the Ethical Return contract by Halborn, the Beanstalk Farms team may spend Beans or USDC, as needed, from its approved budgets if this proposal passes. In the event of a recovery of funds, Beanstalk Farms may also allocate Beans or USDC from its approved budgets for work on how to best handle such a recovery for Beanstalk users, as detailed in the “Economic Rationale” section above. 

Consequently, no Beans will be minted upfront with this proposal. Note that Beanstalk Farms is already afforded the flexibility of being able to use its approved budgets on expenditures outside of the scope of contributor pay, as described under the [BFBP-C](https://docs.bean.money/governance/proposals#bfbp) section in Beanstalk’s Farmers Almanac documentation.

Neither Hats Finance nor Sync will receive any upfront payment for advancing this proposal, and will only be rewarded per below if Beanstalk recovers ETH/funds, either directly via the Ethical Return contract, or via some other manner as a result of the higher bounty proposed by this BOP.

Note this proposed reward is less than the 10% reward that was offered by Beanstalk Farms on April 25th via the above-referenced tweet to any group that facilitated a recovery of funds shortly after the exploit, to be shared equally among all parties. Thus, Beanstalk would keep the remaining portion after accounting for the 40% offered bounty, such that its net recovered amount would equal 58.2% of the ETH/funds sent back to the Ethical Return smart contract.

Hats Finance will be allocated 1% of the recovered ETH/funds for developing the Ethical Return contract, providing guidance to the Beanstalk Farms development team on the deployment of the Ethical Return contract, and for helping drive awareness of the recovery initiative among white hats in its network and beyond through its various communications and social media channels.

Sync will be allocated 1% of the recovered ETH/funds for originating this collaboration, securing the support of the Hats Finance team without any upfront fee for their services, proposing the idea to the Beanstalk community for discussion, and leading the compilation of the BOP from its initial draft to its final form in coordination with the Hats Finance team. 

Lastly, Beanstalk Farms will be allocated 1% of the recovered ETH/funds for assuming responsibility for the deployment of the Ethical Return contract, the custody of recovered funds in the event a recovery is achieved, and its subsequent handling of such a recovery for Beanstalk users.

## Distribution of Rewards

The reward for Hats Finance will be set in the Ethical Return contract. Hats Finance will receive their reward as ETH from the Ethical Return contract to their designated wallet address after any amount of ETH/funds are recovered by Beanstalk.

The reward for Sync will be issued as a corresponding amount of Beans, to be minted and distributed by Beanstalk Farms as a Silo deposit to his designated wallet address within seventy-two hours after any amount of ETH/funds are recovered by Beanstalk, to equal the reward USD value at the time of its distribution to him.

The reward for Beanstalk Farms will be distributed as ETH or a corresponding amount of minted Beans, per their preference.

In sum, this is a balanced and mutually beneficial reward distribution arrangement that fosters interest in the success of Beanstalk while considering the feedback of the Beanstalk community.

In the event that a white hat hacker in the Hats Finance network or beyond is able to retrieve the exploited funds, then they can return the ETH/funds to the Ethical Return smart contract and retain 40% as a white hat bounty.

Assuming, for example, that 99% of the 24,830 ETH taken in the April exploit is returned to the Ethical Return contract (at least 24,580 ETH), here is a breakdown of how it would be allocated:

1. The exploiter or a white hat hacker that returns the ETH to the Ethical Return contract would receive a 40% bounty back to their address directly via the Ethical Return contract = 9,832 ETH.

2. The beneficiary address designated by Beanstalk Farms to be the recipient of recovered funds would recover 60% for Beanstalk users directly via the Ethical Return contract = 14,748 ETH.

3. Hats Finance would receive 1% of the recovered ETH/funds via the Ethical Return contract to their designated wallet address, equal to 147.48 ETH.

4. Sync would separately receive 1% of the recovered ETH/funds via minted Beans distributed to him as a Silo deposit linked to his designated wallet address by Beanstalk Farms within seventy-two hours of a recovery, equal to the USD value of 147.48 ETH at the time of its distribution to him.

5. Beanstalk Farms would receive 1% of the recovered ETH/funds as either ETH or minted Beans, per their preference, equal to 147.48 ETH or its USD value at the time of its distribution to them.
Thus, after deducting the value of 442.44 ETH for the three above mentioned rewards, Beanstalk would recover 14,305.56 ETH, equal to a net of 58.2% of the 24,580 ETH returned to the Ethical Return contract.
Again, the above hypothetical breakdown assumes a recovery is achieved, and these numbers may vary depending on how much, if any, ETH/funds are ultimately recovered.

## Beans Minted

None.

## Effective

Effective upon commit, and valid through at least the end of 2023 to ensure sufficient time is afforded to white hats to potentially achieve a resolution of this matter in the event that the April exploiter is not cooperative or responsive. If progress towards a resolution is not realized by then, a Beanstalk community member may introduce another BOP to propose canceling out this one, if the Beanstalk DAO seeks to move on at that point.
