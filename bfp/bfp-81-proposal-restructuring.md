# BFP-81: Proposal Restructuring

Proposed: July 23, 2022

Status: Passed

Link: [Snapshot](https://snapshot.org/#/beanstalkfarms.eth/proposal/0xa24c368f08093b8a5e27c0b3ae9296eb60272cddc8882434b02a86152d903e59), [Arweave](https://arweave.net/xw65ZkEw_9PwmNbrHsGsxEdMcbv7r9dG5CusZqdz0So)

---

- [Proposer](#proposer)
- [Summary](#summary)
- [Proposal](#proposal)
    - [BIP Changes Rationale](#bip-changes-rationale)
    - [BOP Changes Rationale](#bop-changes-rationale)
    - [BFP Changes Rationale](#bfp-changes-rationale)
    - [BSP Changes Rationale](#bsp-changes-rationale)
    - [BFCP-A Changes Rationale](#bfcp-a-changes-rationale)
    - [BFCP-B Changes Rationale](#bfcp-b-changes-rationale)
    - [BFCP-C Changes Rationale](#bfcp-c-changes-rationale)
    - [BFRPP Changes Rationale](#bfrpp-changes-rationale)
    - [BFBP-A Changes Rationale](#bfbp-c-changes-rationale)
    - [BFBP-B Changes Rationale](#bfbp-c-changes-rationale)
    - [BFBP-C Changes Rationale](#bfbp-c-changes-rationale)
 - [Additional Amendments](#additional-amendments)

## Proposer

Beanstalk Farms

## Summary

* Clarify the governance and proposal structures for Beanstalk Farms and Bean Sprout.
* Increase the scope of the Beanstalk Farms Committee (BFC) powers to include discretion over the Beanstalk Farms budget outside of just contributor pay.
* Reduce the scope of the BFC powers to no longer have discretion over the distribution of Unripe Beans for contributions pre-Replant—see the _BFRPP Changes Rationale_ section.
* Deprecate Beanstalk Farms Proposals and introduce Beanstalk Operations Proposals.
* Change a handful of terminology updates from BFP-79.

## Proposal

Post-exploit, BFPs were used as a way for Beanstalk Farms to quickly get approval from the DAO for Barn Raise plans, Replant structure like the Beanstalk Community Multisig, etc., despite the fact that they were originally only intended to be used for spending the Beanstalk Farms budget per [BIP-8](https://github.com/BeanstalkFarms/Beanstalk/blob/master/bips/bip-8.md#optimistic-approvals).

In an effort to return to a more well-defined decentralized governance structure, it’s important to codify the governance processes for Beanstalk Farms and Bean Sprout in full (to the extent they can be) and make it such that making significant changes to them requires a significant amount of Stalk to vote For.

Beanstalk Farms proposes the following amendments and additions for proposals moving forward. **Rationales for each change are included below the table. BIPs are included for completeness, but are not part of this proposal, as changing the BIP process would require a BIP.**

<table>
  <tr>
   <td><strong>Proposal</strong>
   </td>
   <td><strong>Purpose</strong>
   </td>
   <td><strong>Where it’s proposed</strong>
   </td>
   <td><strong>Who can propose</strong>
   </td>
   <td><strong>Who can vote</strong>
   </td>
   <td><strong>Voting choices</strong>
   </td>
   <td><strong>Voting period</strong>
   </td>
   <td><strong>How it passes</strong>
   </td>
  </tr>
  <tr>
   <td>BIP (Beanstalk Improvement Proposal)
   </td>
   <td>Protocol changes
   </td>
   <td><a href="https://snapshot.org/#/beanstalkdao.eth/">Beanstalk DAO Snapshot</a>
   </td>
   <td>Anyone with 0.1% of the Stalk supply. Snapshots are submitted on behalf of the proposer by the BCM per its process <a href="https://docs.bean.money/governance/beanstalk/bcm-process#proposing-a-bip">here</a>.
   </td>
   <td>Stalkholders
   </td>
   <td>For, Abstain
   </td>
   <td>1-7 days
   </td>
   <td>>50% of Stalk supply voting For, but can be executed sooner than 7 days if there is a 2/3 supermajority.
   </td>
  </tr>
  <tr>
   <td>BOP (Beanstalk Operations Proposal)
   </td>
   <td>DAO voting on things other than protocol changes
   </td>
   <td><a href="https://snapshot.org/#/beanstalkdao.eth/">Beanstalk DAO Snapshot</a>
   </td>
   <td>Anyone with 0.1% of the Stalk supply. Snapshots are submitted on behalf of the proposer by the BCM per the process in the <em>BOP Changes Rationale</em> section below.
   </td>
   <td>Stalkholders
   </td>
   <td>For, Abstain
   </td>
   <td>7 days
   </td>
   <td>>35% of Stalk supply voting For.
   </td>
  </tr>
  <tr>
   <td>BFP
<p>
(Beanstalk Farms Proposal)
   </td>
   <td>[Deprecated]
   </td>
   <td>N/A
   </td>
   <td>N/A
   </td>
   <td>N/A
   </td>
   <td>N/A
   </td>
   <td>N/A
   </td>
   <td>N/A
   </td>
  </tr>
  <tr>
   <td>BSP (Bean Sprout Proposal)
   </td>
   <td>Bean Sprout budget spending (including hiring proposals)
   </td>
   <td><a href="https://snapshot.org/#/wearebeansprout.eth">Bean Sprout  Snapshot</a>
   </td>
   <td>Bean Sprout Multisig signers and anyone with 0.1% of the Stalk supply.
   </td>
   <td>Stalkholders
   </td>
   <td>For, Against
   </td>
   <td>5 days
   </td>
   <td>Optimistically approved if quorum is not reached. Requires 10% of Stalk to reach quorum, after which a majority vote For is required to pass.
   </td>
  </tr>
  <tr>
   <td>BFCP-A (Beanstalk Farms Committee Proposal)
   </td>
   <td>Adding someone to the BFC
   </td>
   <td><a href="https://snapshot.org/#/beanstalkfarms.eth">Beanstalk Farms Snapshot</a>
   </td>
   <td>Anyone with 0.1% of the Stalk supply.
   </td>
   <td>Stalkholders
   </td>
   <td>For, Against
   </td>
   <td>7 days
   </td>
   <td>25% of Stalk supply to reach quorum, after which a majority vote For is required to pass.
   </td>
  </tr>
  <tr>
   <td>BFCP-B (Beanstalk Farms Committee Proposal)
   </td>
   <td>Removing someone from the BFC.
   </td>
   <td><a href="https://snapshot.org/#/beanstalkfarms.eth">Beanstalk Farms Snapshot</a>
   </td>
   <td>Anyone with 0.1% of the Stalk supply.
   </td>
   <td>Stalkholders
   </td>
   <td>For, Against
   </td>
   <td>7 days
   </td>
   <td>35% of Stalk supply to reach quorum, after which a majority vote For is required to pass.
   </td>
  </tr>
  <tr>
   <td>BFCP-C (Beanstalk Farms Committee Proposal)
   </td>
   <td>Renewing the terms of existing BFC members. BFC members have the option to update their previous hiring proposal terms in a BFCP-C.
   </td>
   <td><a href="https://snapshot.org/#/beanstalkfarms.eth">Beanstalk Farms Snapshot</a>
   </td>
   <td>BFC members, proposed once a quarter in the last month of the quarter (March, June, September, December)
   </td>
   <td>Stalkholders
   </td>
   <td>1 voting choice for each BFC member
   </td>
   <td>7 days
   </td>
   <td>25% of Stalk supply to reach quorum, after which a majority vote For is required to pass (where abstaining on a voting choice is equivalent to voting Against). 
   </td>
  </tr>
  <tr>
   <td>BFRPP (Beanstalk Farms Retroactive Payment Proposal)
   </td>
   <td>Pay contributors in Unripe Beans for pre-Replant contributions
   </td>
   <td><a href="https://snapshot.org/#/beanstalkfarms.eth">Beanstalk Farms Snapshot</a>
   </td>
   <td>Beanstalk Farms, proposed one time within 30 days after Replant
   </td>
   <td>Stalkholders
   </td>
   <td>1 voting choice for each contributor
   </td>
   <td>7 days
   </td>
   <td>15% of Stalk supply to reach quorum, after which a majority vote For is required to pass (where abstaining on a voting choice is equivalent to voting Against). 
   </td>
  </tr>
  <tr>
   <td>BFBP-A (Beanstalk Farms Budget Proposal)
   </td>
   <td>Hiring a contributor /updating terms of previous hiring proposal
   </td>
   <td><a href="https://snapshot.org/#/beanstalkfarmsbudget.eth">Beanstalk Farms Budget Snapshot</a>
   </td>
   <td>BFC members
   </td>
   <td>BFC members
   </td>
   <td>For, Against
   </td>
   <td>5 days
   </td>
   <td>At least 3 votes For, but can be overridden by a majority of the BFC voting Against.
   </td>
  </tr>
  <tr>
   <td>BFBP-B (Beanstalk Farms Budget Proposal)
   </td>
   <td>Dismissing a contributor 
   </td>
   <td><a href="https://snapshot.org/#/beanstalkfarmsbudget.eth">Beanstalk Farms Budget Snapshot</a>
   </td>
   <td>BFC members
   </td>
   <td>BFC members
   </td>
   <td>For, Against
   </td>
   <td>5 days
   </td>
   <td>A majority of the BFC voting For or if 3 BFC members have voted For, whichever is more. 
   </td>
  </tr>
  <tr>
   <td>BFBP-C (Beanstalk Farms Budget Proposal)
   </td>
   <td>Beanstalk Farms budget spending outside of hiring contributors
   </td>
   <td><a href="https://snapshot.org/#/beanstalkfarmsbudget.eth">Beanstalk Farms Budget Snapshot</a>
   </td>
   <td>BFC members
   </td>
   <td>BFC members
   </td>
   <td>For, Against
   </td>
   <td>5 days
   </td>
   <td>At least 3 votes For, but can be overridden by a majority of the BFC voting Against.
   </td>
  </tr>
</table>

### BIP Changes Rationale

No changes.

### BOP Changes Rationale

There is a class of proposals that the DAO should vote on that are not necessarily protocol changes a la BIPs. An example may be, but is not limited to, ratifying the set of disclosures about the risks of interacting with Beanstalk.

These should not be optimistically approved like BFPs were, and should require a significant Stalk threshold to pass.

The following are the processes in place for community members to submit a BOP and coordinate with the BCM to submit a Snapshot proposal on the Beanstalk DAO Snapshot page:

1. A proposer must own 0.1% of the total Stalk supply in order to propose a BOP. The proposer shall verify that they meet the Stalk ownership threshold by creating and verifying a signature on etherscan. The steps to create and verify a signature on etherscan can be found[ here](https://info.etherscan.com/verify-signature-tool/). The proposer will then reach out to the Mods on Discord and from there, the BCM will verify that the address that signed the message has sufficient Stalk.
2. The proposer will publish the written proposal in a dedicated channel in the Beanstalk Discord. For assistance creating a channel on Discord, contact the Mods on Discord.
3. The written proposal shall be discussed in the Discord channel for a sufficient amount of time. What constitutes sufficient will be at the sole discretion of the BCM, but the BCM must formally propose the BOP on Snapshot on behalf of the proposer within 2 weeks of the creation of the dedicated Discord channel, unless the proposer decides to withdraw their proposal.
4. The BCM will submit a Snapshot of the written proposal to formally begin the voting period.

### BFP Changes Rationale

If the BFC has discretion on how to use the Beanstalk Farms budget via BFBPs, it becomes unclear what BFPs are for.

As mentioned, it’s important to codify the governance processes for Beanstalk Farms and Bean Sprout in full (to the extent they can be), such that making significant changes to them requires a significant amount of Stalk voting For. In contrast, BFPs were optimistically approved per [BIP-8](https://github.com/BeanstalkFarms/Beanstalk/blob/master/bips/bip-8.md#optimistic-approvals).

BOPs will be used as the procedure moving forward for having the DAO vote on proposals that are not necessarily protocol code changes and also outside of the scope of BSPs, BFCPs and BFBPs.

Therefore, BFPs are deprecated.

### BSP Changes Rationale

Per BFP-80, anyone with 0.1% of Stalk can propose a BSP. Now, any address on the Bean Sprout Multisig can also propose a BSP. Anyone on the Bean Sprout Multisig should also have significant alignment with the success of Beanstalk and accordingly an effective use of the Bean Sprout budget. Thus, this change is implemented primarily for flexibility purposes.

BSPs are still optimistically approved, but only require a 10% quorum and majority voting Against in order to veto. This allows those that are pleased with the actions of Bean Sprout to remain passive, unless there is a quorum voting Against a particular proposal.

### BFCP-A Changes Rationale

No changes. The term of a BFC member whose BFCP-A passes still ends two quarters after the end of the current quarter.


### BFCP-B Changes Rationale

The quorum required to remove a BFC member has been increased from 25% to 35%. Similar to the BFBP-B quorum being higher than the BFBP-A quorum, this facilitates permissionless contributions as there must be a higher consensus to remove someone than to add someone to the BFC.


### BFCP-C Changes Rationale

The BFC renewal Snapshot must go up in the last month of each quarter—March, June, September and December. This allows the DAO to evaluate each BFC member based on the current quarter.

The BFCP-C quorum is increased from 15% to 25% to match the quorum requirement for BFCP-As. Extending a term on the BFC should require the same quorum as getting added to the BFC.

BFP-80 stated that BFC members whose renewal proposal passes “will have their term extended by two quarters after _<span style="text-decoration:underline;">the end of the current quarter</span>_, or extended to a maximum of three quarters after the end of the current quarter, whichever is shorter.” This was a typo. BFC members whose renewal proposal passes will have their term extended by two quarters after _<span style="text-decoration:underline;">the end of their current term</span>_, with a maximum term length of three quarters after the end of the current quarter.

### BFRPP Changes Rationale

The BFC no longer has discretion over distributing Unripe Beans to contributors for pre-Replant contributions. Given that the BFC is formed after contributions made between the exploit and Replant, it is more appropriate to have the DAO vote to grant Unripe Bean payments.

Beanstalk Farms will propose a one-time, multi-choice proposal within 30 days after Replant called a Beanstalk Farms Retroactive Payment Proposal, or BFRPP. Each voting choice on the BFRPP corresponds to a contributor who is proposing to get retroactively compensated in Unripe Beans.

The BFRPP will contain each contributor's contributions and an associated number of Unripe Beans that the contributor is proposing to be paid.  

Quorum for the BFRPP is 15% of the Stalk supply, after which a majority vote For is required to pass for each individual contributor (where abstaining on a voting choice is equivalent to voting Against). 

### BFBP-A Changes Rationale

Beanstalk Farms Staff Proposals have been renamed to Beanstalk Farms Budget Proposals to account for the increase in scope (see the BFBP-C Changes Rationale section).

### BFBP-B Changes Rationale

No changes. The BFC will still maintain a public list of contributors per BFP-80. Also per BFP-80, BFBP-Bs still will not mention the contributor’s name, but instead will include a hash, where the input of the hash indicates the contributor being dismissed. The input of the hash will be shared only with the BFC and the contributor in question. This allows the contributor to verify the result of the vote while preserving their privacy during the voting period and if the vote fails. A BFBP-B will also be proposed in circumstances where a contributor is leaving Beanstalk Farms voluntarily.

### BFBP-C Changes Rationale

BFBP-Cs extend the scope of BFC powers to discretion over budget spending outside of contributor pay. In practice, contributor pay (which the BFC was given discretion over per BFP-80) has been and will likely continue to be the majority of Beanstalk Farms expenditures, but there are occasional other payments outside of that scope like paying for upgrades to the subgraph, paying an artist for art on the website, etc. Instituting BFBP-Cs allows the BFC to move quickly on budget decisions.

![image](https://user-images.githubusercontent.com/28496268/185805108-0b8c9eb8-e1a2-4bef-ad43-c041f0607f7c.png)

## Additional Amendments

1. In all cases where Stalkholders vote on a proposal or a proposal requires a minimum Stalk threshold to propose, if the Stalk supply is compromised in a flash loan or other governance attack at the time of proposal, the proposal is void. 
2. In all cases where a proposal requires a minimum Stalk threshold to propose, the minimum Stalk threshold must be met _at the time of proposal_.
3. Additions/removals/rotations of Beanstalk Farms Multisig or Bean Sprout Multisig signers must be proposed either via BOP or as part of a BIP. A BIP/BOP is not required if a signer voluntarily leaves a multisig (nor for rotating in a backup signer in the event of a voluntary departure). 
4. Any proposals that do not pass for any reason may be proposed again after the end of the original voting period.
5. A BFCP-B is not required if the BFC member voluntarily leaves Beanstalk Farms. The BFC member will create a record of their departure by creating and verifying a signature on etherscan using the address they used to propose and vote on BFBPs.
6. Beanstalk Farms often sends funds to reimburse contributors for various software expenses. A BFBP-C is not required for transactions valued at under 4000 Beans or USDC. Similarly, a BSP is not required for Bean Sprout to send transactions valued at under 4000 Beans.
7. Bean Sprout hiring proposals can either be proposed as part of a BIP, BOP or BSP.
8. Moving forward, any changes to the governance structure outlined in this proposal must be changed via BIP or BOP.
9. Because terminology changes can only be made via BIP or BOP moving forward, we are taking the opportunity in this final BFP to propose a handful of minor amendments to the terminology updates in BFP-79 before the Replant:
    1. Receivable Assets → Claimable Assets
        1. Rationale: “Receive” became an issue given that this functionality is adjacent to “Sending” Deposits in the Silo (it is confusing to have Sending and Receiving be next to each other when they are not related or opposites). _Claiming _assets after Withdrawing is also more intuitive than _Receiving_.
    2. Fertilized Sprouts → Rinsable Sprouts
        2. Rationale: Given that Sprouts that are redeemable for Beans must be _Rinsed_, it is more appropriate for _Rinsable_ Sprouts to be Rinsed, similar to how Harvestable Pods must be Harvested.
    3. Downpour → Oversaturation
        3. Rationale: The Pod Rate being less than 5% is more relevant to the state of the Farm (like how many Pods are in the ground) than the weather above. Thus, it’s more appropriate that it Floods on the Farm after X Seasons of _being Oversaturated_ than after X Seasons of _Downpour_. 
