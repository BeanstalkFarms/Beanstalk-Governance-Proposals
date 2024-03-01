# BIR-13: Minting Unripe LP During Convert

Proposed: February 25, 2024

Status: Passed

Link: [Snapshot](https://snapshot.org/#/beanstalkbugbounty.eth/proposal/0x971214b1ae7847c743704c3014c92f47e8c8a151cc786a4f0519c2c8624beecd), [Arweave](https://arweave.net/4UVXaWu6R58-apc_iqZULgPujMoJenZW1TWypgnBzPY)

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

* Mint 10,000 Beans to the whitehat that reported the issue related to minting additional Unripe LP during Conversions; and
* Mint 1,000 Beans to Immunefi's address in order to cover the 10% fee.

## Links

* [BIC Process](https://docs.bean.money/governance/beanstalk/bic-process)
* [Safe Transaction](https://app.safe.global/transactions/tx?safe=eth:0xa9bA2C40b263843C04d344727b954A545c81D043&id=multisig_0xa9bA2C40b263843C04d344727b954A545c81D043_0x17f906751890a8fbe660554952d5669e80bde7b534baef7989ac3148090a22c7)

## Bug

A bug was submitted through Immunefi that allows an Unripe Bean Depositor to mint additional Unripe LP by sending Beans or ETH to the BEAN:ETH Well before Converting. This is because Unripe Bean to Unripe LP Converts are implemented with the `sync` Well Implementation function rather than `addLiquidity`.

## Fix

Change `sync` to `addLiquidity` in `LibFertilizer.addUnderlying`.

Given the low impact and likelihood of this issue being exploited (it is unprofitable to execute), the BCM determined that an EBIP is not necessary. The goal is to include this fix in an upcoming BIP.

## Determination

The BIC determined that the practicable economic damage of this issue is zero given that an attack would never be profitable. However, the most appropriate impact in scope for this report is "Illegitimate minting of protocol native assets", i.e., High severity, as a result of the potential for minting additional Unripe LP.

For these reasons, the BIC has determined that this bug report be rewarded 10,000 Beans.

* Potential practicable economic damage: $0
* Impact: High — _Illegitimate minting of protocol native assets_
* Entitled to reward: Yes

## Beans Minted

The `init` function on the following `InitMint` contract is called:
* [`0x077495925c17230E5e8951443d547ECdbB4925Bb`](https://etherscan.io/address/0x077495925c17230E5e8951443d547ECdbB4925Bb#code)

We propose 10,000 Beans are minted to the following address in order to pay the bounty to the whitehat:
* [`0xd7E167AB2A4bC910619ACB0CC36B7707Ab8816d6`](https://etherscan.io/address/0xd7E167AB2A4bC910619ACB0CC36B7707Ab8816d6)

We propose 1,000 Beans are minted to the following address in order to pay the 10% fee to Immunefi:
* [`0x2BC5fFc5De1a83a9e4cDDfA138bAEd516D70414b`](https://etherscan.io/address/0x2BC5fFc5De1a83a9e4cDDfA138bAEd516D70414b)
