# BFP-74: Various BCM and BIP Process Amendments

Proposed: June 10, 2022

Status: Passed

Link: [Snapshot](https://snapshot.org/#/beanstalkfarms.eth/proposal/0x9709ce7c77006acf37ab89621369e6e36f26a892a297fc40373c34a19a2ec228)

---

- [Proposer](#proposer)
- [Summary](#summary)
- [Proposal](#proposal)

## Proposer

Beanstalk Farms

## Summary

* Remove BCM signers that get through 2 months or 2 votes (whichever happens first) without performing any of their signer duties.
* No Snapshot is required for adding/removing/rotating BCM signers.
* Clarify where BFP, BSP and BIP Snapshots are proposed.

## Proposal

**BCM and BIP process amendments**

**1)** In BFP-73, it was written that a signer shall lose their role in case they get through 2 months or 2 votes (whichever takes longer) without performing any of their signer duties.

This was a typo. Instead, if a signer gets through 2 months or 2 votes (**whichever happens first**, rather than whichever takes longer) without performing any of their signer duties, they will be removed from the BCM.

**2)** In BFP-73, it was written that adding/removing/rotating multisig signers would require a Snapshot with a voting period that lasts 2 days.

Given the anonymous nature of the signers, Stalk holders shouldn’t be burdened with voting on adding/removing/rotating signers. Each time this occurs, Publius will announce in Discord that a change was made with a link to the new hashed list of signers on-chain.

<table>
  <tr>
   <td><strong>Transaction</strong>
   </td>
   <td><strong>Snapshot?</strong>
   </td>
   <td><strong>Voting Period</strong>
   </td>
  </tr>
  <tr>
   <td>Proposing BIP
   </td>
   <td>Yes
   </td>
   <td>Up to 7 days as outlined in the <em>Voting for BIPs </em>section
   </td>
  </tr>
  <tr>
   <td><strong>Adding/removing/rotating multisig signers</strong>
   </td>
   <td><strong>No</strong>
   </td>
   <td><strong>N/A</strong>
   </td>
  </tr>
  <tr>
   <td>Emergency hotfix (needs public notification)
   </td>
   <td>No
   </td>
   <td>N/A
   </td>
  </tr>
  <tr>
   <td>Emergency Pause (needs public notification)
   </td>
   <td>No
   </td>
   <td>N/A
   </td>
  </tr>
  <tr>
   <td>Cancel transaction
   </td>
   <td>No
   </td>
   <td>N/A
   </td>
  </tr>
</table>

**Snapshot pages**

This Snapshot page, [https://snapshot.org/#/beanstalkfarms.eth](https://snapshot.org/#/beanstalkfarms.eth), is the page where BFPs and BSPs are submitted. In general, these are related to budget and process decisions for Beanstalk Farms and Bean Sprout. Beanstalk was much smaller when this page was created, and thus the minimum Stalk required to propose a Snapshot was 2500 Stalk. This minimum will be changed to 25000 Stalk. Post-Replant, in order to allow enough time for people to vote, each Snapshot on this page will be for a minimum of 5 days (120 hours).

There is a new Snapshot page where BIPs will be proposed at [https://snapshot.org/#/beanstalkdao.eth](https://snapshot.org/#/beanstalkdao.eth). Only the BCM will be able to propose Snapshots on this page, per the process outlined in BFP-73. This is a measure taken to prevent BIP Snapshots from being proposed without associated GitHub merge requests. In an effort to promote a sufficiently permissionless proposal process, any community member who wishes to propose a BIP can follow the BIP Proposal Process from BFP-73.
