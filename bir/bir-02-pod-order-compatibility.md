# BIR-2: V1 Pod Order Backwards Compatibility 

Proposed: November 18, 2022

Status: Passed

Link: [Snapshot](https://snapshot.org/#/beanstalkbugbounty.eth/proposal/0xb07c3ff8112c01849681a62980b5499599990e26e01d9ca244fd6483783ece2c), [Arweave](https://arweave.net/zayRM8ATyNJR_1n5xRRQ2gkaOdADRbeOX3q7CyfYPHg)

---

- [Proposer](#proposer)
- [Bug Bounty Process Note](#bug-bounty-process-note)
- [Links](#links)
- [Bug](#bug)
- [Fix](#fix)
- [Determination](#determination)
- [Bounty Amount](#bounty-amount)

## Proposer

Beanstalk Immunefi Committee

## Bug Bounty Process Note

Per the process outlined in [BIR Execution](https://docs.bean.money/governance/beanstalk/bic-process#execution), once a BIR passes, the Beanstalk Community Multisig (BCM) executes it by:
* Minting Beans to the whitehat's address in order to cover the bug bounty; and
* Minting Beans to Immunefi's address in order to cover the 10% fee.

## Links

* [Gnosis Transaction](https://app.safe.global/eth:0xa9bA2C40b263843C04d344727b954A545c81D043/transactions/tx?id=multisig_0xa9bA2C40b263843C04d344727b954A545c81D043_0x4c242b8f0a8d0bf9a3a72c5e4812ccb515a44aef8fb5b6997d22b91b5e2db05b)
* [EBIP-4: Remove V1 Pod Order Functions](https://arweave.net/WE73eyNcrbkCSZBAQerylbQ8VAoPjD0HBhVM6-OARVg)

## Bug

Cancelling V1 Pod Orders that were created before and Cancelled after [BIP-29](https://arweave.net/OfWylBAxD5KyGJBWrQto2EyeYpzc-MqfaroXMQ1bk5w) was committed would return the number of Pods Ordered in Beans times the price per Pod, rather than the Beans initially locked in the V1 Pod Order. 

## Fix

Update `s.podOrders[id]` for the `id`s of the 3 remaining V1 Pod Orders from the number of Pods Ordered to the number of Beans locked. This was fixed in [EBIP-6](https://arweave.net/o0cB9SKHQq1y_KqIRZ8oK-xLjtSiTXuiT5dGmkwxygI).

Notably, because the whitehat returned the extra 9,228.946824 Beans they received from Cancelling their Pod Order to Beanstalk (see [return transaction](https://etherscan.io/tx/0x09ad148ba695a05d08fd0726b9927411f94eceede13a730d4550f52ce9dc5e7d)), no Beans need to be minted to Beanstalk as part of the fix.

## Determination

As described in [EBIP-4](https://arweave.net/WE73eyNcrbkCSZBAQerylbQ8VAoPjD0HBhVM6-OARVg), this bug would have only resulted in an excess of ~105,121 Beans being distributed to the 3 remaining addresses that created a V1 Pod Order before [BIP-29](https://arweave.net/OfWylBAxD5KyGJBWrQto2EyeYpzc-MqfaroXMQ1bk5w) was committed. This loss would have only been realized if Farmers withdrew **all remaining assets** from Beanstalk (Farm balances, the Silo, etc.). 

Note: the report did not come with a Proof of Concept nor code implementing the fix.

* Potential practicable economic damage: None, due to the reasons regarding the likelihood of losses being realized outlined above. 
* Impact: Medium (_Smart contract unable to operate due to lack of token funds_)
* Entitled to reward: Yes

## Bounty Amount

* 11,000 Beans will be minted to the whitehat's address for the bounty; and
* 1,100 Beans will be minted to Immunefi's address in order to cover the 10% fee.
