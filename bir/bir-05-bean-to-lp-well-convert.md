# BIR-5: Bean to LP Well Convert

Proposed: October 30, 2023

Status: Passed

Link: [Snapshot](https://snapshot.org/#/beanstalkbugbounty.eth/proposal/0x32b1d929858088dc7a42527ba1b7c4cf87f9e15f8f70756d6032214479e8ec1d), [Arweave](https://arweave.net/_nR90oRTxWJ6LXSrCaA85zyEIPk-rOd5Km6hnKGShGI)

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

Mint 100,000 Beans to the whitehat that reported the issue fixed in [EBIP-10](https://arweave.net/im3PLE28EkO_eMo4fPmtcTYBJFRErxZ_44I_LWPDIB8).

## Links

Per the process outlined in [BIR Execution](https://docs.bean.money/governance/beanstalk/bic-process#execution), once a BIR passes, the Beanstalk Community Multisig (BCM) executes it by minting Beans to the whitehat's address in order to cover the bug bounty. No fee is minted in this instance because this bug was reported directly to the BIC outside of the Immunefi platform.

* [Safe Transaction](https://app.safe.global/transactions/tx?safe=eth:0xa9bA2C40b263843C04d344727b954A545c81D043&id=multisig_0xa9bA2C40b263843C04d344727b954A545c81D043_0xdcf5acc203b023c3d4c7486f226a545f94a66adb4294021efd012fd6ecf692b2)
* [EBIP-10: Fix Bean to LP Well Convert](https://arweave.net/im3PLE28EkO_eMo4fPmtcTYBJFRErxZ_44I_LWPDIB8)

## Bug

In Wells (i.e., only BEANETH currently), Farmers could Convert Deposited Beans to Deposited LP tokens past peg. Additionally, If a Farmer had enough Deposited Beans to Convert past peg, it was possible for that Farmer to Convert Deposited Beans to Deposited LP tokens up to the total amount of Beans in the Beanstalk contract. 

This was because in `LibWellConvert._wellAddLiquidityTowardsPeg`, Beanstalk was Converting (1) the amount the user input (`beans`), rather than (2) the minimum of the amount the user input and the amount required to Convert to peg (`beansConverted`).

## Fix

Upgrade Beanstalk to only allow Converts from Beans to LP tokens in Wells up to (2), i.e., `beansConverted`.

This was fixed in EBIP-10.

## Determination

The BIC determined that the funds at risk were all of the Beans in the Beanstalk contract (~23M) given that an attacker could have Converted all of these Beans into BEANETH Well LP tokens, removed the liquidity and sold the Beans. The amount of ETH that could be stolen combined with the subsequent crash in the Bean price would have resulted economic damage of over $11M.

Given this, the BIC has determined that this report would qualify for the max reward on Immunefi of 1.1M Beans. However, the whitehat has graciously accepted an offer of 100,000 Beans as a reward, given their long term alignment with Beanstalk.

* Potential practicable economic damage: >$11M
* Impact: Critical (_Direct theft of any user funds, whether at-rest or in-motion, other than unclaimed yield_)
* Entitled to reward: Yes

## Beans Minted

The `init` function on the following `InitMint` contract is called:
* [`0x077495925c17230E5e8951443d547ECdbB4925Bb`](https://etherscan.io/address/0x077495925c17230E5e8951443d547ECdbB4925Bb#code)

We propose 100,000 Beans are minted to the following address in order to pay the bounty to the whitehat:
* [`0x7E38De5f3053d579474A02ccc257A2F3C626AC75`](https://etherscan.io/address/0x7E38De5f3053d579474A02ccc257A2F3C626AC75)
