# BIR-11: Stalk Ownership Griefing (Reproposal)

Proposed: February 5, 2024

Status: Failed

Link: [Snapshot](https://snapshot.org/#/beanstalkbugbounty.eth/proposal/0xc9c24e53808bf7de86f54c27a2d0a71b9dcbb916b74e9493d521e6b64fe8d63e)

---

- [Proposer](#proposer)
- [Summary](#summary)
- [Links](#links)
- [Bug](#bug)
- [Fix](#fix)
- [Determination](#determination)
- [Beans Minted](#beans-minted)

*Note: This is a reproposal of [BIR-10](https://snapshot.org/#/beanstalkbugbounty.eth/proposal/0x3247308cde62b6bc55a82e61224887817f2f81dabba0dfd007a97178585b65bd), where the BIC informally agreed on the bounty amount but the Snapshot vote was not able to get 4 signatures before end of the Voting Period.*

## Proposer

Beanstalk Immunefi Committee

## Summary

* Mint 1,000 Beans to the whitehat that reported the Stalk ownership griefing issue; and
* Mint 100 Beans to Immunefi's address in order to cover the 10% fee.

## Links

* [BIC Process](https://docs.bean.money/governance/beanstalk/bic-process)
* [Safe Transaction](https://app.safe.global/transactions/tx?safe=eth:0xa9bA2C40b263843C04d344727b954A545c81D043&id=multisig_0xa9bA2C40b263843C04d344727b954A545c81D043_0x917e0ab28c89154944155f104a467f88eff35a672bc3308a3eb73c1343369470)

## Bug

By calling `transferDeposit` with an `amount` of `0`, anyone is able to steal 1 `root` (an internal accounting variable used to track Stalk ownership) at a time from any other user for the cost of gas.

## Fix

Add a `require` statement that checks that the `amount` in `transferDeposit` is greater than 0.

Given the low impact and likelihood of this issue being exploited (it is unprofitable to execute), the BCM determined that an EBIP is not necessary. The goal is to include this fix in an upcoming BIP.

## Determination

The BIC determined that the impact of this issue is low given that `roots` cannot be redeemed for any underlying value and the costs of any form of an "attack" would not be profitable by a significant margin. Thus, the most applicable impact in scope is griefing and any "attack" related to this bug report would have an extremely low likelihood. 

For these reasons, the BIC has determined that this bug report be rewarded 1,000 Beans.

* Potential practicable economic damage: N/A
* Impact: Medium — _Griefing (e.g. no profit motive for an attacker, but damage to the users or the protocol)_
* Entitled to reward: Yes

## Beans Minted

The `init` function on the following `InitMint` contract is called:
* [`0x077495925c17230E5e8951443d547ECdbB4925Bb`](https://etherscan.io/address/0x077495925c17230E5e8951443d547ECdbB4925Bb#code)

We propose 1,000 Beans are minted to the following address in order to pay the bounty to the whitehat:
* [`0x47E6B4a6cB47EB7C7D30820c7C460183Cf7Abd5b`](https://etherscan.io/address/0x47E6B4a6cB47EB7C7D30820c7C460183Cf7Abd5b)

We propose 100 Beans are minted to the following address in order to pay the 10% fee to Immunefi:
* [`0x2BC5fFc5De1a83a9e4cDDfA138bAEd516D70414b`](https://etherscan.io/address/0x2BC5fFc5De1a83a9e4cDDfA138bAEd516D70414b)
