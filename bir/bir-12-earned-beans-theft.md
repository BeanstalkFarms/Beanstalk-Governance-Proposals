# BIR-12: Earned Beans Theft

Proposed: February 5, 2024

Status: Passed

Link: [Snapshot](https://snapshot.org/#/beanstalkbugbounty.eth/proposal/0x66b7fa7d5cab3f97f8a685004bf465b8ab603edc55bb9d5b24ea92d64173a50a), [Arweave](https://arweave.net/CSs4__jXKCiuU7KXd5xvZ_vwDLOqqZXW8aFo5o7loTE)

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

* Mint 50,000 Beans to the whitehat that reported the theft of Earned Beans issue; and
* Mint 5,000 Beans to Immunefi's address in order to cover the 10% fee.

## Links

* [BIC Process](https://docs.bean.money/governance/beanstalk/bic-process)
* [Safe Transaction](https://app.safe.global/transactions/tx?safe=eth:0xa9bA2C40b263843C04d344727b954A545c81D043&id=multisig_0xa9bA2C40b263843C04d344727b954A545c81D043_0xa7f7da59294dc0ceb17e973f24bf7de446a4f54edd0fc5b8e514c487c4cb47d3)

## Bug

A bug was submitted through Immunefi that allows a user to potentially steal unclaimed Earned Beans during the 10 block Vesting Period at the beginning of the Season, depending on the value of `s.newEarnedStalk`. 

This was due to an inconsistency in the number of `roots` minted when Depositing compared to the number of `roots` burnt when Withdrawing. By repeatedly Withdrawing and Depositing Beans, an attacker could increase the Stalk/`root` ratio and thus inflate their total Stalk balance before then calling `plant`. This attack could have been profitable based on the value of `s.newEarnedStalk`.

## Fix

Given that the Seed Gauge System replaces the Vesting Period, in the meantime the best route is to remove the Vesting Period logic altogether and revert when `withdrawDeposit(s)` is called during the first 10 blocks of the Season. 

At the time of execution of this EBIP, based on the value stored at `s.newEarnedStalk`, this attack was not profitable. However, the attack was profitable at various times over the last few months (i.e., some Earned Beans could be stolen for profit).

This was fixed in [EBIP-14](https://github.com/BeanstalkFarms/Beanstalk/pull/762).

## Determination

The BIC determined that it is not practical to precisely quantify the practicable economic damage in this report as a result of the exploit being more or less profitable at different times.

The report points out that during various Seasons over the last few months, the attack has been profitable by various amounts, including a particular Season (and following Seasons until certain conditions changed) where up to 20,000 Beans could have been stolen in each block during the 10 block Vesting Period.

Given this economic damage that could occur at various points in time, the BIC has determined that this bug report be rewarded 50,000 Beans.

* Potential practicable economic damage: N/A
* Impact: High (_Theft of unclaimed yield_)
* Entitled to reward: Yes

## Beans Minted

The `init` function on the following `InitMint` contract is called:
* [`0x077495925c17230E5e8951443d547ECdbB4925Bb`](https://etherscan.io/address/0x077495925c17230E5e8951443d547ECdbB4925Bb#code)

We propose 50,000 Beans are minted to the following address in order to pay the bounty to the whitehat:
* [`0xd7E167AB2A4bC910619ACB0CC36B7707Ab8816d6`](https://etherscan.io/address/0xd7E167AB2A4bC910619ACB0CC36B7707Ab8816d6)

We propose 5,000 Beans are minted to the following address in order to pay the 10% fee to Immunefi:
* [`0x2BC5fFc5De1a83a9e4cDDfA138bAEd516D70414b`](https://etherscan.io/address/0x2BC5fFc5De1a83a9e4cDDfA138bAEd516D70414b)
