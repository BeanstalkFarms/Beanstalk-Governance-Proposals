# BIP-35: Stalk Delegation and Process Amendments

Proposed: April 26, 2023

Status: Proposed

Link: [Snapshot](https://snapshot.org/#/beanstalkdao.eth/proposal/0x61bc097da95b6dfefe8fdb2dc9ed606f281442320a720a1a09c8c664a08a6db4)

---

- [Proposer](#proposer)
- [Summary](#summary)
- [Links](#links)
- [Problem](#problem)
- [Proposed Solution](#proposed-solution)
- [Rationale](#rationale)
- [Contract Changes](#contract-changes)
- [Beans Minted](#beans-minted)
- [Effective](#effective)

## Proposer

Beanstalk Farms

Proposer Wallet: [0xf1a621fe077e4e9ac2c0cefd9b69551db9c3f657](https://etherscan.io/verifySig/17216)

## Summary

* Enable Stalk delegation on the various Beanstalk Snapshot spaces; and
* Make various updates to Beanstalk governance detailed below, including updating the [Beanstalk Community Multisig (BCM) Process](https://docs.bean.money/almanac/governance/beanstalk/bcm-process), [Beanstalk Immunefi Committee (BIC) Process](https://docs.bean.money/almanac/governance/beanstalk/bic-process), [Immunefi Bug Bounty Program](https://immunefi.com/bounty/beanstalk/) and [Beanstalk DAO Disclosures](https://docs.bean.money/almanac/disclosures).

## Links

* [BIP-35 GitHub PR](https://arweave.net/WOdo880CKI63hAgQP-_ZtUwom-y0VyPfZ5c2dFrErDQ)

## Problem

Participation from Stalkholders is crucial for Beanstalk governance. However, at present, each Stalkholder is required to vote on every governance proposal in order to have their vote represented. Some Stalkholders might not have adequate knowledge about the proposals, may not have sufficient time or motivation to vote, and/or might not be aware of them, resulting in reduced voting participation.

Beanstalk governance, the BCM Process, the BIC Process, the Immunefi Bug Bounty Program and the Beanstalk DAO Disclosures can all be updated to reflect the current state of Beanstalk and its ecosystem.

## Proposed Solution

### Stalk Delegation

By enabling Stalk delegation on the various Beanstalk ecosystem Snapshot spaces ([beanstalkdao.eth](https://snapshot.org/#/beanstalkdao.eth), [beanstalkfarms.eth](https://snapshot.org/#/beanstalkfarms.eth) and [wearebeansprout.eth](https://snapshot.org/#/wearebeansprout.eth)), Stalkholders will be able to delegate their votes to any other Farmer. The delegate can then vote on their behalf in governance proposals. Stalk must be delegated on each Snapshot space individually.

If Farmer A delegates to Farmer B and both of them vote, then the delegated voting power is not calculated—in this instance the votes of the two Farmers will be calculated as if the delegation did not happen. The vote of B will be counted if A does not vote.

### Process Amendments 

#### Beanstalk Governance

We propose the following list of changes to Beanstalk governance:
* A Stalkholder's vote for a given proposal is counted as their Stalk at the beginning of the Voting Period that still exists;
* The Voting Period opens when the Snapshot proposal for a BIP can be voted on and ends after 7 days or once a two-thirds supermajority is reached;
* If at the end of the Voting Period:
    * Less than or equal to half of total Stalk is voting For the BIP, it fails, or
    * More than half of total Stalk is voting For the BIP, it passes;
* If at any time 24 hours or more after the beginning and before the end of the Voting Period more than two-thirds of the total Stalk is voting For the BIP, the BCM can execute the BIP on-chain;
* The proposer must have 0.1% of total Stalk (including delegated Stalk) at the beginning and end of a Voting Period where Stalkholders vote (i.e., BIPs, BOPs, BFCPs and BSPs) in order for the proposal to pass;
* Only BIPs can (1) change the Beanstalk protocol, (2) mint Beans and/or (3) change Beanstalk governance;
* BOPs are proposals for the DAO to agree on processes that do not involve changes that can only be made in BIPs;
* Voting choices for a BIP are For, Abstain and Against (where Abstain and Against have no effect);
* Voting choices for a BOP are For and Against;
* BOPs and BFCP-Bs must have >35% of total Stalk voting For and a majority of participating Stalk voting For at the end of the Voting Period in order to pass;
* BFCP-As must have >25% of total Stalk voting For and a majority of participating Stalk voting For at the end of the Voting Period in order to pass;
* The Voting Period for BFBPs is 2 days; 
* Any proposal that proposes for another party to perform work upon, or at any point after, the passage of the proposal must have that party's explicit approval; and
* BFCP-Cs do not need to be proposed at any particular frequency (BFC member term lengths still apply).

The updated Beanstalk-related governance processes can be read in the new Proposals documentation [here](https://arweave.net/vOGUuWPvvxNDGdttqCdki8Job_qq9f74qvALYxnVO8o).

#### BCM Process

We propose the following list of changes to the BCM Process:

* Integrate all of the changes included in the above Beanstalk Governance section;
* Add the `addUnripeToken` and `addFertilizerOwner` functions to the list of owner functions for completion;
* Require a BIP for non-emergency changes to the m-of-n configuration of the BCM, but allow emergency changes to the m-of-n configuration without a BIP in order to remove rogue Signers before a replacement Signer can be selected;
* Create significantly more clear and permissionless BIP and BOP proposal processes;
* Require that written proposals for BIPs have a Contract Changes section that lists the facets and initialization contract address; and
* Require that written proposals for BIPs have a Beans Minted section that describes the number of Beans minted by the execution of the proposal;
* Make it clear that BIPs may have multiple transactions if made explicit in the written proposal;
* Add relevant BCM processes for verifying and executing Beanstalk Immunefi Responses (BIRs);
* Remove the requirement for Signers to rotate wallets every 2 months;
* More thoroughly document the processes for BCM Signers to verify and sign transactions;
* Specify that in cases where functions must be removed immediately to protect Beanstalk, BCM Signers are not required to submit an Etherscan verified message; and
* Specify that in no instance shall a majority of the BCM keys be held by Beanstalk Farms contributors.

The updated BCM Process can be read [here](https://arweave.net/g6hiZUDixN6SIYCEqe9HO8pAhsjAN4tzh8sfMUXRBag).

#### BIC Process

The following is a list of proposed changes to the BIC Process:

* Add a Fix section that describes the process that is followed in order to fix a reported issue;
* Specify that the potential economic damage determination is only required in BIRs for smart contract bug reports qualified as High or Critical Impact;
* Clarify the process that the BIC follows after a fix is implemented and a determination has been made by the BIC;
* Require that written proposals for BIRs have a Beans Minted section that describes the number of Beans minted by the execution of the proposal;
* Make it explicit that the BIC has the ability to update the Immunefi Bug Bounty Program in the following ways, without the need for a BOP or BIP:
    * Adding new assets as in-scope, including contracts that have been audited and/or have the potential to attract a meaningful amount of Beans/BDV;
    * Updating the list of code in-scope that has been audited but has not yet been deployed on-chain;
    * Adding documentation and links; and
    * Updating language to improve clarity.

The updated BIC Process can be read [here](https://arweave.net/7zkZms8WC7lMAkHGkDvUQCONdOcQrI8SkirBXEIBRMc).

#### Beanstalk Farms Budget Custody

We propose that members of the Beanstalk Farms Committee (BFC) may each custody of up to 4,000 Beans from the Beanstalk Farms budget to make payments for expenses incurred by Beanstalk Farms.

We propose that the following Farmers serve as backup signers for the Beanstalk Farms Multisig (BFM), in no particular order:
* pizzaman1337
* CanadianBennett
* MrMochi

#### Immunefi Bug Bounty Program

The following is a list of proposed changes to the Immunefi Bug Bounty Program:

* Specify that Beans/BDV at risk are the primary consideration when determining practicable economic damage;
* Specify that Circulating non-Bean assets at risk may qualify for a maximum of up to 50% of the potential reward outlined in the Rewards by Threat Level section, as determined by the BIC; 
* Specify that unexpected outcomes (like loss of funds) due to misuse of Pipeline do not qualify as valid bug reports; and
* Add the Beanstalk UI as in-scope of the program.

The updated Immunefi Bug Bounty Program can be read [here](https://arweave.net/kQcgKTy9iUogBmMhx10FPo8DWV1d0xmzg_3Qfj0tLF8).


#### Disclosures

The following is a list of proposed changes to the Beanstalk DAO Disclosures:
* Add the following disclosure about the Beanstalk Subgraph: 

    THE APP.BEAN.MONEY FRONTEND DEPENDS ON THE BEANSTALK SUBGRAPH FOR DISPLAYING VARIOUS ON-CHAIN DATA. THERE IS NO GUARANTEE THAT SUBGRAPH DATA IS ACCURATE.
    The Beanstalk UI hosted at app.bean.money depends on the Beanstalk Subgraph for displaying data, particularly on the Market and Analytics pages. The Beanstalk Subgraph is primarily developed and deployed by Beanstalk Farms.
    By default the Beanstalk UI uses a version of the subgraph hosted by Beanstalk Farms, which can be censored. The subgraph that the Beanstalk UI uses can be adjusted in the settings.
    
* Add a note about the Beanstalk SDK being unaudited in Disclosure #18; and
* Allow Beanstalk Farms to modify the Disclosures that pertain to Beanstalk development tooling as they change without the need for a BIP or BOP.

The updated Beanstalk DAO Disclosures can be read [here](https://arweave.net/VpB8mrlsXsYT0VptAAhjHUwqD_TmvTI5aIklXTNzdlo).

## Rationale

### Stalk Delegation

Allowing Stalkholders to delegate their votes to other Farmers can increase voting participation as a delegate can potentially be more knowledgeable or motivated to participate in governance than the delegator. Thus, Stalk delegation can promote more effective and representative protocol governance.

### Process Amendments

The proposed changes to Beanstalk governance increase the clarity of language around various proposal processes and increase the flexibility of Beanstalk Farms proposal processes. 

The proposed changes to the BCM Process significantly increase the quality of documentation for and the permissionlessness of the BIP and BOP proposal processes. 

The proposed changes to the BIC Process significantly increase the quality of documentation for the BIC operating processes and gives the BIC more flexibility in updating the Immunefi Bug Bounty Program.

The proposed changes to the custody of the Beanstalk Farms budget will permit timely payments of small amounts in a swift fashion (without requiring the signatures of BFM signers).

The proposed changes to the Immunefi Bug Bounty Program further refine what bug reports are considered valid and how the BIC determines practicable economic damage, which improves the bug reporting experience for Immunefi whitehats. The proposed changes also add the Beanstalk UI as in-scope to reward whitehats for finding interface-level vulnerabilities.

The proposed changes to the Beanstalk DAO Disclosures reflect the current state of Beanstalk development tooling.

## Contract Changes

None.

## Beans Minted

None.

## Effective

Immediately upon passage.
