# BIP-41: Immunefi Program Update

Proposed: February 18, 2024

Status: Passed

Link: [Snapshot](https://snapshot.org/#/beanstalkdao.eth/proposal/0x8f2599d129a01bc5c668698fbcc053f9aca241c6f07e8f3cc117ef203c67276f)

---

- [Proposer](#proposer)
- [Summary](#summary)
- [Links](#links)
- [Introduction](#introduction)
- [Amounts](#amounts)
- [BIC Process](#bic-process)
- [Program Structure](#program-structure)
- [BICM Signers](#bicm-signers)
- [Rationale](#rationale)
- [Contract Changes](#contract-changes)
- [Beans Minted](#beans-minted)

## Proposer

Beanstalk Immunefi Committee

Proposer Wallet: [0x4a24e54a090b0fa060f7faaf561510775d314e84](https://etherscan.io/verifySig/35871)

## Summary

* Increase the number of remaining Beans allocated to the Beanstalk bug bounty program on Immunefi (currently 1,389,315 Beans) to 2,500,000 Beans in order to continue incentivizing whitehats to report vulnerabilities in Beanstalk, Basin and Pipeline;
* Mint these 2,500,000 Beans to the Beanstalk Immunefi Committee Multisig (BICM) to execute bug bounty payments via an Immunefi Vault;
* Mint an additional 37,800 Beans to the BICM to pay for 1 year of an annual Immunefi subscription that waives the 10% fee for bounty payments and hires Immunefi experts to triage bug reports;
* Update the terms of the bug bounty program established in [BIP-26](https://arweave.net/GnnM7WxvhttHhIKkjffhOHgAQKR6esbjNzIzlnWS8Y4) for consistency and clarity; and
* Rotate members on the BIC and add a minimum bounty threshold to the Beanstalk Immunefi Response (BIR) process.

## Links

* [BIP-41 GitHub PR](https://github.com/BeanstalkFarms/Beanstalk/pull/771)
* [Safe Transaction](https://app.safe.global/transactions/tx?safe=eth:0xa9bA2C40b263843C04d344727b954A545c81D043&id=multisig_0xa9bA2C40b263843C04d344727b954A545c81D043_0xb36d91ebe219bef8e5a4cfcfdefd1de2445b9c84dadf833bc90f4bb8826fc445)

## Introduction

Security is paramount to Beanstalk's success. A critical component of security is a post-deployment bug bounty program, which was established with [Immunefi](https://immunefi.com/bounty/beanstalk/) in BIP-26 to incentivize whitehat hackers to report vulnerabilities in Beanstalk (and now, in Pipeline and Basin as well).

The Beanstalk bug bounty program on Immunefi has been very successful to date, rewarding 1,473,350 Beans to whitehats in total across 11 valid bug reports, 3 of which could have potentially caused significant damage to Beanstalk.

Relevant links:
* [BIC Dashboard](https://docs.bean.money/almanac/governance/beanstalk/bic-dashboard#beans-minted)
* [Past bounty payouts](https://snapshot.org/#/beanstalkbugbounty.eth)
* [Past bug reports](https://community.bean.money/bug-reports)

## Amounts

We propose a total of 2,500,000 Beans are minted to be allocated towards valid bug reports according to the rules outlined in the bug bounty program. 

We propose that the BCM is no longer authorized to mint Beans per BIRs. Note that BIP-26 authorized the BCM to mint up to 3,000,000 Beans per BIRs, of which 1,610,685 were minted. The remaining 1,389,315 Beans were never minted and are not in circulation.

We also propose that an additional 37,800 Beans are minted to pay for 1 year of an annual Immunefi subscription that waives the 10% fee on all bounty payments and hires Immunefi experts to triage bug reports. This is a ~34% discount off of their standard offer.

## BIC Process

The following is a list of proposed changes to the BIC Process:

* Update language to indicate that bug bounty payments are now made via an Immunefi Vault* funded by the BICM, a 4-of-7 Safe with the BIC members as signers;
* Grant the BIC the ability to execute bug bounty payments under 50,000 Beans without formally voting on a BIR on Snapshot;
* Grant the BIC the ability to update the Assets in Scope, Impacts in Scope and the Out of Scope Rules sections without requiring a BIP or BOP; and
* Change the two-thirds majority required by the BIC to execute a bug bounty payment to a majority.

*The [Immunefi Vaults System](https://immunefisupport.zendesk.com/hc/en-us/articles/18233838041745-Vaults-System) is a Safe multisig that increases transparency between projects and whitehats by allowing whitehats to view how much a project has specifically deposited for potential bug bounty reward payments via the Immunefi UI.

The updated BIC Process can be read [here](https://arweave.net/3vfKSRU72BcSgeC0pH5SHW9UzonLl1NmmMGfnc-8t1o).

## Program Structure

The following is a list of proposed changes to the Beanstalk Immunefi Bug Bounty Program:
* Change rewards for High smart contract vulnerabilities from being capped at (1) the lower of 100% of practicable economic damage and 100,000 Beans, to (2) the lower of 10% of practicable economic damage and 100,000 Beans;
* Remove governance voting result manipulation from Impacts in Scope (as governance voting is currently off-chain); and
* Remove the requirement to provide code implementing a fix as part of the bug report (in order to encourage swift reporting of vulnerabilities).

The updated Immunefi Bug Bounty Program can be read [here](https://arweave.net/ddA6z28UiKLkxfAv-VcNPP_fEcjAZs7jII7iZfgMdu0).

## BICM Signers

We propose the following seven members for the BIC, who also serve as signers on the BICM:

* aloceros (new member)
* Brean (current member)
* Chaikitty (new member)
* deadmanwalking (new member)
* funderberker (current member)
* mod323 (current member)
* pizzaman1337 (current member)

The following people serve as backups for the BIC, in no particular order:
* guy
* MrMochi
* uncoolzero

Rotating members (to a backup member listed here) on the BIC requires a majority vote of the BIC, for which a Snapshot proposal is not necessary.

## Rationale

Security is paramount to the success of Beanstalk. This bounty program is competitive with the largest programs currently on Immunefi, making it likely to continue to attract whitehats if well capitalized.

Paying the annual subscription of 37,800 Beans to Immunefi is likely to decrease the amount paid in fees by the DAO through the bug bounty program. For reference, in 2022 the DAO paid 21,035 Beans in fees (the bug bounty program was only live for ~3 months in 2022) and in 2023 the DAO paid 111,000 Beans in fees. Additionally, Immunefi's managed triage service is expect to save significant resources in triaging bug reports that would otherwise be going towards implementing the specifications outlined in [the various RFCs on GitHub](https://github.com/BeanstalkFarms/Beanstalk/issues). The 37,800 Bean amount is a ~34% discount off of their standard annual subscription plan.

We also propose that an additional 37,800 Beans are minted to pay for 1 year of an annual Immunefi subscription that waives the 10% fee on all bounty payments and hires Immunefi experts to triage bug reports. 


Immunefi reports that projects that have an Immunefi Vault with significant funds are more likely to receive bug reports because whitehats will be confident that the project has enough funds to pay for bugs.

The proposed changes to the BIC Process gives the BIC more flexibility in updating the bug bounty program and permits timely payments of small bounty amounts in a swift fashion.

The proposed changes to the Immunefi Bug Bounty Program further refine what bug reports are considered valid and how the BIC determines practicable economic damage, which improves the bug reporting experience for Immunefi whitehats.

## Contract Changes

None.

## Beans Minted

The `init` function on the `InitMint` contract at [0x077495925c17230E5e8951443d547ECdbB4925Bb](https://etherscan.io/address/0x077495925c17230E5e8951443d547ECdbB4925Bb#code) is called.

We propose a total of 2,537,800 Beans are minted for the Immunefi bug bounty program to the BICM address ([0x879c8B99430F28C4d297BD479Cd43396b4aCF697](https://etherscan.io/address/0x879c8B99430F28C4d297BD479Cd43396b4aCF697)) upon the execution of this BIP.

## Effective

Immediately upon commitment.
