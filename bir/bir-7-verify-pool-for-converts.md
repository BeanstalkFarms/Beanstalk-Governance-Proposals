# BIR-7: Verify Whitelisted Pool for Converts

Proposed: November 22, 2023

Status: Passed

Link: [Snapshot](https://snapshot.org/#/beanstalkbugbounty.eth/proposal/0x3df4899741db63e66e51939166df737bdb1166be18633dd2dd78fdce45dd22bd)

---

- [Proposer](#proposer)
- [Summary](#summary)
- [Links](#links)
- [Bug](#bug)
- [Fix](#fix)
- [Determination](#determination)
- [Beans Minted](#beans-minted)

## Proposer

Beanstalk Immunefi Committee

## Summary

* Mint 1,100,000 Beans to the whitehat that reported the issue that led to the removal of the `convert` function in [EBIP-12](https://arweave.net/AFsSqT2HE67IHqtxafvbluoZApdyofiHmvwGmzjTUPU); and
* Mint 110,000 Beans to Immunefi's address in order to cover the 10% fee.

## Links

* [BIC Process](https://docs.bean.money/governance/beanstalk/bic-process)
* [Safe Transaction](https://app.safe.global/transactions/tx?safe=eth:0xa9bA2C40b263843C04d344727b954A545c81D043&id=multisig_0xa9bA2C40b263843C04d344727b954A545c81D043_0x8fdb3f518d97fa09543efb9880e256283395d8f6014aa72fe7bbf2b4ae883eb4)
* [EBIP-12: Remove Convert](https://arweave.net/zAtoxAMBSIVvJTPz45nXPT0lUbgU5Krw5MJ-SWwEmSI)
* [EBIP-13: Re-Add Convert](https://arweave.net/zKpHhC4c8NhJecrEGHQ8F_vhrLVVEqdGKrQODSoIKbg)

## Bug

Since Replant and prior to this EBIP, Converts did not validate that the pool being Converted in is whitelisted, which would have allowed an attacker to Convert all Beans in the the Beanstalk contract into their own Bean Deposits (which could then be Withdrawn and sold).

## Fix

Add `require` statements in `LibWellConvert` that verify that the Well being Converted in is whitelisted.

This was fixed in EBIP-13.

## Determination

The BIC determined that the funds at risk were all of the Beans in the Beanstalk contract (~22.8M at the time of the report) given that an attacker could have Converted all of these Beans into their own Bean Deposits (which could then be Withdrawn and sold).

Given this, the BIC has determined that this report qualifies for the max reward on Immunefi of 1.1M Beans. 

* Potential practicable economic damage: >$11M
* Impact: Critical (_Direct theft of any user funds, whether at-rest or in-motion, other than unclaimed yield_)
* Entitled to reward: Yes

## Beans Minted

The `init` function on the following `InitMint` contract is called:
* [`0x077495925c17230E5e8951443d547ECdbB4925Bb`](https://etherscan.io/address/0x077495925c17230E5e8951443d547ECdbB4925Bb#code)

We propose 1,100,000 Beans are minted to the following address in order to pay the bounty to the whitehat:
* [`0x278cdd1F886F967Ab1EA7b942575B08FAB50383D`](https://etherscan.io/address/0x278cdd1F886F967Ab1EA7b942575B08FAB50383D)

We propose 110,000 Beans are minted to the following address in order to pay the 10% fee to Immunefi:
* [`0x2BC5fFc5De1a83a9e4cDDfA138bAEd516D70414b`](https://etherscan.io/address/0x2BC5fFc5De1a83a9e4cDDfA138bAEd516D70414b)
