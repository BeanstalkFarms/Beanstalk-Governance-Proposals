# BIR-8: Root Permit Redemption Griefing

Proposed: January 2, 2024

Status: Passed

Link: [Snapshot](https://snapshot.org/#/beanstalkbugbounty.eth/proposal/0x75bc2bffeb4c38e3bc64b0bde09b4545a523f66334d61e0866db2d884c56162f), [Arweave](https://arweave.net/1CjdtLXICRWG4EBG74w0kMApZzUhftiapBUVn0-H6UM)

---

- [Proposer](#proposer)
- [Summary](#summary)
- [Links](#links)
- [Bug](#bug)
- [Determination](#determination)
- [Beans Minted](#beans-minted)

## Proposer

Beanstalk Immunefi Committee

## Summary

* Mint 1,000 Beans to the whitehat that reported the griefing issue in the Root contract; and
* Mint 100 Beans to Immunefi's address in order to cover the 10% fee.

## Links

* [BIC Process](https://docs.bean.money/governance/beanstalk/bic-process)
* [Safe Transaction](https://app.safe.global/transactions/tx?safe=eth:0xa9bA2C40b263843C04d344727b954A545c81D043&id=multisig_0xa9bA2C40b263843C04d344727b954A545c81D043_0xacfe6a078a0ca2cabc63d1cc1c1b653473d1a18594773d569bc22a7061796ac9)

## Bug

### Expected Behavior

The `redeemWithFarmBalancePermit` function in the Root contract utilizes the permit function so that approve and redeem operations can happen in a single transaction.

### Attack

ERC20Permit uses the nonces mapping for replay protection. Once a signature is verified and approved, the nonce increases, invalidating the same signature being replayed.

`redeemWithFarmBalancePermit` expects the holder to sign their tokens and provide the signature to the contract as part of the permit data. When a `redeemWithFarmBalancePermit` transaction is in the mempool, an attacker can take this signature and call the permit function on the token themselves.

Since this is a valid signature, the token accepts and increases the nonce. This makes the spender's transaction fail whenever it gets mined.

## Determination

Based on the bug bounty program, this submission's ( Smart Contract - Medium ) reward is based on a set of internal criteria established by the BIC (with a minimum reward of USD 1 000), primarily taking into account the exploitability of the bug, the impact it causes and likelihood of the vulnerability presenting itself.

The BIC determined that the impact of this issue is low given the that the Root contract is not functional (Roots cannot be redeemed as a result of the Beanstalk Silo V3 upgrade) and the low value of assets in the contract.

Given this, the BIC has determined that this report qualifies for a reward of 1,000 Beans. 

* Potential practicable economic damage: N/A
* Impact: Medium _(Griefing — no profit motive for an attacker, but damage to the users or the protocol)_
* Entitled to reward: Yes

## Beans Minted

The `init` function on the following `InitMint` contract is called:
* [`0x077495925c17230E5e8951443d547ECdbB4925Bb`](https://etherscan.io/address/0x077495925c17230E5e8951443d547ECdbB4925Bb#code)

We propose 1,000 Beans are minted to the following address in order to pay the bounty to the whitehat:
* [`0x5BDe67C6850Ca8D7B324a2b740C7F6E99Aa694C0`](https://etherscan.io/address/0x5BDe67C6850Ca8D7B324a2b740C7F6E99Aa694C0)

We propose 100 Beans are minted to the following address in order to pay the 10% fee to Immunefi:
* [`0x2BC5fFc5De1a83a9e4cDDfA138bAEd516D70414b`](https://etherscan.io/address/0x2BC5fFc5De1a83a9e4cDDfA138bAEd516D70414b)
