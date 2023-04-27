# BOP-3: Form the BeaNFT DAO

Proposed: April 26, 2023

Status: Proposed

Link: [Snapshot](https://snapshot.org/#/beanstalkdao.eth/proposal/0xf2a75c3148e4c979b4a1370f8babc549e4bb5625ebdd3b81e8f2a8907c3893ca)

---

- [Proposer](#proposer)
- [Summary](#summary)
- [Problem](#problem)
- [Proposed Solution](#proposed-solution)
- [Rationale](#rationale)
- [Effective](#effective)

## Proposer

blc, Beanstalk Farms

Proposer Wallet: [0xbc4de0a59d8af0af3e66e08e488400aa6f8bb0fb](https://etherscan.io/verifySig/17229)

## Summary

Form the BeaNFT DAO by:

- Introducing a suitable governance structure that allows the DAO to create and upgrade BeaNFT collections; and
- Forming a community-run multisig that custodies ownership of all past and future BeaNFT collections on behalf of the DAO.

## Problem

There is currently no transparent governance process that allows BeaNFT holders to vote on whether a new collection can be created or whether an existing collection can be upgraded. Past BeaNFT collections are currently custodied across various EOAs.

## Proposed Solution

Form the BeaNFT DAO, the organization that can create and upgrade BeaNFT collections.

### Proposal Structure

BeaNFT Proposals (BNPs) are the proposals through which the BeaNFT DAO votes on the direction of BeaNFTs (new collections, collection upgrades, etc.).

A BeaNFT holder’s vote is counted in proportion to their number of BeaNFTs when the BNP is submitted to Snapshot (i.e., 1 BeaNFT = 1 vote). BeaNFT holders are able to delegate their votes to any other Farmer.

Anyone with at least 0.1% of the BeaNFT supply can propose a BNP. BNPs have two voting choices, For and Against, and require 15% of the BeaNFT supply voting For to reach quorum (see *Supply and Participation* section). Once a quorum is reached, a majority vote For is required to pass. The Voting Period for BNPs is 7 days.

#### Proposal Composition

A BNP to create a new BeaNFT collection should include, at a minimum: (1) criteria to qualify for a BeaNFT in the new collection and (2) any rarity boost criteria. Including high level information about the theme of the collection, whether qualifying Farmers will be required to mint them, etc. is recommended. After the passage of the BNP, it is on the proposer to see through the completion of the art, the creation and deployment of the contracts, etc. Due to this, there is no guarantee that a new collection gets created after the passage of this type of BNP. 

A BNP seeking to upgrade an existing BeaNFT collection should outline (1) the proposed upgrade, (2) the rationale for the upgrade and (3) any technical criteria required to be implement the upgrade.

A BNP can also be used to change the configuration or signers on the BeaNFT DAO Multisig (see *Custody* section).

#### Supply and Participation

At the time of writing (April 1st, 2023), ~3K BeaNFTs have been minted and total of ~3.9K have been generated (about 900 Genesis and Winter BeaNFTs have not been minted by qualifying wallets). This means 0.1% of the BeaNFT supply is around ~3 BeaNFTs.

Note that qualifying Genesis and Winter BeaNFTs can be minted on the [Beanstalk UI](https://app.bean.money/#/nft), whilst Barn Raise BeaNFTs were minted to qualifying wallets by Beanstalk Farms.

To provide some insight on potential future voting participation:

- In BIPs [31](https://snapshot.org/#/beanstalkdao.eth/proposal/0x184c458cf3f69f4cb62bf92e9f31f873aa852aea3f9d60116e9c6dd9afa4d8ff) and [32](https://snapshot.org/#/beanstalkdao.eth/proposal/0xa23167457ea2be6939f1a296cc14357366d9de995eb0d261bcbcdebf13bad0e8) (neither of which passed), participating Stalkholders held ~18% of the BeaNFT supply; and
- In [BIP-33](https://snapshot.org/#/beanstalkdao.eth/proposal/0x46af2f9d85ad2b9d298ff75737fb35d4f4a617e500647cb73e2bbabd82e6d725) (which passed), participating Stalkholders held ~26% of the BeaNFT supply.

### Custody

Past and future BeaNFT contracts will be custodied by the BeaNFT DAO Multisig (BDM), a multisig wallet with keys held by various community members. The multisig configuration will start as 4-of-7 and can be changed by the BeaNFT DAO through a BNP.

The BDM is not intended to have decision making power. Its role is to enact on-chain the decisions BeaNFT holders make via off-chain voting.

BDM Signers:

- blc
- Brean
- Safisynai
- guy
- Rex
- pizzaman1337
- MrMochi

Backup signers (can be rotated in at the discretion of the BDM without a proposal):

- Silo Chad
- Sowphocles
- CanadianBennett

### Other Notes

Owning a BeaNFT provides a holder access to the BeaNFT Club, a section in the Beanstalk Discord that only BeaNFT holders have access to. Holders can use the `#collabland-join` channel to receive the BeaNFT Club Member Discord role.

## Rationale

By forming the BeaNFT DAO and creating a process for contributing to BeaNFTs, a pathway is forged for holders to propose and implement utility in the BeaNFT ecosystem in a decentralized fashion. Decentralization of custody of the BeaNFT contracts is also significantly improved.

## Effective

Immediately upon passage.
