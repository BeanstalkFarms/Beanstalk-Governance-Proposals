# BFP-80: Beanstalk Farms Committee

Proposed: July 13, 2022

Status: Passed

Link: [Snapshot](https://snapshot.org/#/beanstalkfarms.eth/proposal/0x3bf1e9ea8167019635787f44662096a1747b44f17c5e1571e33506d34453e889), [Arweave](https://arweave.net/waSAVeykqOSaFEL_jvQ7PYJaaN7LGllJyf1R_edCJVc)

---

- [Proposer](#proposer)
- [Summary](#summary)
- [Proposal](#proposal)
    - [BFC Powers](#bfc-powers)
    - [BFC Selection](#bfc-selection)
    - [Pre-Replant Pay](#pre-replant-pay)
    - [Recap](#recap)

## Proposer

Beanstalk Farms

## Summary

* Outline a governance structure for Beanstalk Farms.
* Establish the Beanstalk Farms Committee (BFC), the committee that has decision making power for personnel decisions within Beanstalk Farms. 

## Proposal

Historically there has been minimal process for bringing on contributors to Beanstalk Farms, and no process for letting them go. The last couple months post-exploit have been an opportunity to reflect on what these processes should look like. In preparation for the future Q3 Budget BIP, it is prudent to come up with a clear structure for governance of and within Beanstalk Farms.

The Beanstalk Farms Committee, or BFC, will be a group of Beanstalk Farms contributors that collectively have the power to hire and let go of contributors within Beanstalk Farms. In order to have clarity on how personnel decisions are made, some notion of authority within Beanstalk Farms is necessary. However, it is critical that these processes are as transparent and permissionless as possible. 

The BFC structure and process are proposed with the following considerations in mind:

* Creating a clear and transparent process for hiring/letting go of contributors; 
* Allowing the BFC to make decisions quickly;
* Creating accountability for the BFC to the Beanstalk DAO; and
* Continuing to facilitate a permissionless process to contribute to Beanstalk through Beanstalk Farms.

### BFC Powers

The BFC has decision making power for personnel decisions within Beanstalk Farms. 

Beanstalk Farms Staff Proposals, or BFSPs, will be proposed on a new Snapshot page. BFSPs are proposals to hire or let go of a Beanstalk Farms contributor that is not on the BFC. Any changes to the terms of an existing contributor’s hiring proposal (like a pay increase, for example) will be made via a new hiring proposal. Only BFC members can propose and vote on BFSPs, and each member has 1 vote.

The voting period for BFSPs shall be 5 days. A BFSP to hire or change the terms of a hiring proposal passes if at the end of the voting period there are at least 3 votes in favor, but this can be overridden by a majority of the BFC voting against. A BFSP should contain salary information, the proposer, the proposed hire, information about what makes the person a good candidate for their role, their proposed responsibilities, etc.

Once approved via BFSP, a contributor’s term length is indefinite unless otherwise specified (in general, grant work should go through Bean Sprout). In order to maintain an effective, high quality organization, non-BFC contributors who demonstrate a pattern of underperformance may be let go from Beanstalk Farms by the BFC. A BFSP to let go of a contributor passes if at the end of the voting period a majority of the BFC has voted in favor or if 3 BFC members have voted in favor, whichever is more. 

A BFSP to let go of a contributor will not mention the contributor’s name, but instead will include a hash, where the input of the hash indicates the contributor being dismissed. The input of the hash will be shared only with the BFC and the contributor in question. This allows the contributor to verify the result of the vote while preserving their privacy during the voting period and if the vote fails. A BFSP will also be proposed in circumstances where a contributor is leaving Beanstalk Farms voluntarily.

The BFC will maintain a public list of active contributors.

### BFC Selection

The BFC will consist of nobody to start. 

Upon the passage of BFP-80, Beanstalk Farms Committee Proposals (or BFCPs) can be proposed on the Beanstalk Farms Snapshot page. BFCPs are proposals to add someone to the BFC, remove someone from the BFC, renew the existing set of BFC members, or in a one-time special case, retroactively compensate the initial set of BFC members for pre-Replant contributions.

**Process**

Anyone can propose a BFCP, and the threshold for doing so will be 0.1% of total Stalk. The voting period for BFCPs shall be 7 days. BFCPs require a 25% quorum. Once a quorum is reached, it is the outcome of the Snapshot proposal that determines whether the proposed person is added to the BFC. 

There is no minimum or maximum number of BFC members. With fewer than 3 members, the BFC has no power to hire or let go of contributors; no maximum number facilitates permissionlessness.

**Proposal Content**

BFCPs to add a committee member should be titled “BFCP-X: Add [Person] to the BFC” with “For” and “Against” as voting choices on the Snapshot proposal. A BFCP to add someone to the BFC is also that person’s hiring proposal. Therefore, a BFCP to add someone to the BFC shall contain salary information at minimum, but should also contain information about what makes the person a good candidate for the BFC, their proposed responsibilities within Beanstalk Farms, etc.

**Term length**

Once approved via BFCP, a BFC member serves on the committee for the remainder of the current quarter (end of March, June, September, or December) in addition to the following two quarters. There is no term limit for BFC members. 

Every quarter, the DAO will have an opportunity to signal support for each of the members of the BFC. This will manifest as a multi-choice BFCP proposed by the BFC where each voting choice corresponds to a current BFC member (e.g. “Renew Bean Farmer X”, “Renew Bean Farmer Y”, etc). Members whose corresponding voting choice reaches 15% of total Stalk in favor will have their term extended by two quarters after the end of the current quarter, or extended to a maximum of three quarters after the end of the current quarter, whichever is shorter.

BFC members can still be removed from the BFC in advance of the end of their term via a BFCP. BFCPs to remove a committee member should be titled “BFCP-X: Remove [Person] from the BFC” with “For” and “Against” as voting choices on the Snapshot proposal. The same BFCP voting period and quorum applies to proposals to remove a contributor from the BFC. BFSPs that were proposed by the removed BFC member are still valid.

### Pre-Replant Pay

The payment information included in BFCPs and BFSPs will strictly cover payment for work from contributors after Beanstalk is Replanted. Of course, a Q3 Budget BIP must pass thereafter in order to fulfill these payments.

Once the Unripe Bean token is deployed, the BFC will have unilateral authority to distribute any of the 2,062,760.53269 Unripe Beans in the Beanstalk Farms multisig to compensate contributors who are not on the BFC for contributions pre-Replant. 

After Unripe Beans are distributed to non-BFC contributors at the discretion of the BFC, the initial set of BFC members will submit a proposal to get approval from the DAO to pay themselves Unripe Beans for their pre-Replant contributions. 

This will be a one-time, special instance of a BFCP that grants the initial set of BFC members the power to pay themselves according to the Unripe Bean amount specified for each contributor in the proposal. This will be a multi-choice proposal where each voting choice corresponds to one of the initial BFC members. Members whose corresponding voting choice reaches 15% of Stalk in favor will receive the corresponding Unripe Bean payment outlined in the proposal for that BFC member.

### Recap

BFCPs will be proposed on the Beanstalk Farms Snapshot page alongside BFPs and BSPs. The proposal threshold on this page will be 0.1% of total Stalk.

There will be a handful of Snapshot pages that coordinate governance of Beanstalk, Beanstalk Farms, and Bean Sprout. There are no changes to the BIP proposal process but it is included in the table for completion.

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
   <td><a href="https://snapshot.org/#/beanstalkdao.eth">Beanstalk DAO Snapshot</a>
   </td>
   <td>Anyone with 0.1% of the Stalk supply. Snapshots are submitted on behalf of the proposer by the BCM per the process <a href="https://docs.bean.money/governance/beanstalk-community-multisig/bcm-process#proposing-a-bip">here</a>.
   </td>
   <td>1-7 days
   </td>
   <td>50% of Stalk supply <em>voting For</em>.
   </td>
  </tr>
  <tr>
   <td>BFP (Beanstalk Farms Proposal)
   </td>
   <td>BF budget spending outside of paying contributors
   </td>
   <td><a href="https://snapshot.org/#/beanstalkfarms.eth">Beanstalk Farms Snapshot</a>
   </td>
   <td>Anyone with 0.1% of the Stalk supply. 
   </td>
   <td>5 days
   </td>
   <td>Optimistic approval and tiered quorum per <a href="https://github.com/BeanstalkFarms/Beanstalk/blob/master/bips/bip-8.md#optimistic-approvals">BIP-8</a>.
   </td>
  </tr>
  <tr>
   <td>BFCP (Beanstalk Farms Committee Proposal)
   </td>
   <td>Adding/removing someone to/from the BFC
   </td>
   <td><a href="https://snapshot.org/#/beanstalkfarms.eth">Beanstalk Farms Snapshot</a>
   </td>
   <td>Anyone with 0.1% of the Stalk supply. 
   </td>
   <td>7 days
   </td>
   <td>25% of Stalk supply to reach quorum, after which a majority vote in favor is required to pass. BFC members also have terms that end (a BFCP is not required to remove them). 
   </td>
  </tr>
  <tr>
   <td>BFCP (Beanstalk Farms Committee Proposal)
   </td>
   <td>Renewing the terms of existing BFC members
   </td>
   <td><a href="https://snapshot.org/#/beanstalkfarms.eth">Beanstalk Farms Snapshot</a>
   </td>
   <td>Members of the BFC, proposed once a quarter
   </td>
   <td>7 days
   </td>
   <td>Any BFC member that receives 15% of Stalk voting in favor gets their term extended by two quarters after the end of the current quarter, or extended to a maximum of three quarters after the end of the current quarter, whichever is shorter.
   </td>
  </tr>
  <tr>
   <td>BFCP (Beanstalk Farms Committee Proposal)
   </td>
   <td>Pay initial set of BFC members Unripe Beans for pre-Replant contributions
   </td>
   <td><a href="https://snapshot.org/#/beanstalkfarms.eth">Beanstalk Farms Snapshot</a>
   </td>
   <td>Members of the BFC, proposed one time
   </td>
   <td>7 days
   </td>
   <td>Any BFC member that receives 15% of Stalk voting in favor will receive their corresponding Unripe Bean payment.  
   </td>
  </tr>
  <tr>
   <td>BSP (Bean Sprout Proposal)
   </td>
   <td>BS budget spending
   </td>
   <td><a href="https://snapshot.org/#/beanstalkfarms.eth">Beanstalk Farms Snapshot</a>
   </td>
   <td>Anyone with 0.1% of the Stalk supply. 
   </td>
   <td>5 days
   </td>
   <td>Optimistic approval and tiered quorum per <a href="https://github.com/BeanstalkFarms/Beanstalk/blob/master/bips/bip-8.md#optimistic-approvals">BIP-8</a>.
   </td>
  </tr>
  <tr>
   <td>BFSP (Beanstalk Farms Staff Proposal)
   </td>
   <td>Hiring/change terms of hiring proposal/letting go of contributors
   </td>
   <td>Beanstalk Farms Staff Snapshot (to be created)
   </td>
   <td>Members of the BFC
   </td>
   <td>5 days
   </td>
   <td>Hire: At least 3 votes <em>For</em>, but can be overridden by a majority of the BFC voting against.
<p>
Changing terms of hiring proposal: At least 3 votes <em>For</em>, but can be overridden by a majority of the BFC voting against. 
<p>
Let go: A majority of the BFC voting in favor or if 3 BFC members have voted in favor, whichever is more. 
   </td>
  </tr>
</table>
