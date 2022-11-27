# BIR-3: transferTokenFrom External Balances 

Proposed: November 23, 2022

Status: Proposed

Link: [Snapshot](https://snapshot.org/#/beanstalkbugbounty.eth/proposal/0x63fddb1e9c22a5b98defb607a5b6520444a0ef08736445238132c702a2a0e4fa), [Arweave](https://arweave.net/ObqSYNnh5OIopDNHlJmwByFgT-qYjQV60oeYli5Q-fU)

---

- [Proposer](#proposer)
- [Bug Bounty Process Note](#bug-bounty-process-note)
- [Links](#links)
- [Bug](#bug)
- [Fix](#fix)
- [Determination](#determination)
- [Beans Minted](#beans-minted)

## Proposer

Beanstalk Immunefi Committee

## Bug Bounty Process Note

Per the process outlined in [BIR Execution](https://docs.bean.money/governance/beanstalk/bic-process#execution), once a BIR passes, the Beanstalk Community Multisig (BCM) executes it by:
* Minting Beans to the whitehat's address in order to cover the bug bounty; and
* Minting Beans to Immunefi's address in order to cover the 10% fee.

## Links

* [Safe Transaction](https://app.safe.global/eth:0xa9bA2C40b263843C04d344727b954A545c81D043/transactions/tx?id=multisig_0xa9bA2C40b263843C04d344727b954A545c81D043_0x0c104fb1b6216a31827d2572bcc5b4cfef9174a5da2c904dfe5657146c2ef712)
* [EBIP-5: Remove transferTokenFrom Function](https://arweave.net/rihDlnrjmAlFHRmsR3OeP3svGXcxFy_h1dEb0akpylk)

## Bug

In `transferTokenFrom(...)`, only the allowance for Farm (`INTERNAL`) balances from `msg.sender` was checked, not Circulating (`EXTERNAL`) balances. Therefore, anyone could successfully call the `transferTokenFrom(...)` function with `EXTERNAL` as `fromMode`, their own address as `recipient` and the address of a Farmer who had Circulating assets that were approved to be used by Beanstalk as `sender`.

## Fix

Change `transferTokenFrom(...)` to `transferInternalTokenFrom(...)` such that the function always transfers with `INTERNAL` `fromMode`.

This was fixed in [EBIP-6](https://arweave.net/o0cB9SKHQq1y_KqIRZ8oK-xLjtSiTXuiT5dGmkwxygI).

## Determination

The BIC determined that:

* The funds at risk were roughly $3.1M (see [EBIP-5](https://arweave.net/rihDlnrjmAlFHRmsR3OeP3svGXcxFy_h1dEb0akpylk));
* Any funds at risk were **assets that users had approved to be used by Beanstalk**;
* **At most 537k Beans** could have been used to remove value from the BEAN:3CRV liquidity pool; and
* Any non-Bean funds at risk could not have resulted in any loss of value in Beanstalk (apart from marginal amounts of BEAN3CRV LP, urBEAN and urBEAN3CRV).

While the purpose of the bug bounty program is to increase the security of Beanstalk and is not necessarily concerned with non-Bean assets outside of Beanstalk, the BIC acknowledges that a large portion of the funds at risk due to this vulnerability fall into the latter category.

Given this, the BIC has determined that the Bean portion of the funds at risk be rewarded the full 10% reward and the remaining non-Bean assets outside of Beanstalk at risk be rewarded 5%:

537,000 * 0.1 + ((3,100,000 - 537,000) * 0.05) = 181,850 Beans. 

* Potential practicable economic damage: ~$537,000 of damage to Beanstalk and ~$2,563,000 of other damage
* Impact: Critical (_Direct theft of any user funds, whether at-rest or in-motion, other than unclaimed yield_)
* Entitled to reward: Yes

## Beans Minted

The `init` function on the following `InitMint` contract is called:
* [`0x077495925c17230E5e8951443d547ECdbB4925Bb`](https://etherscan.io/address/0x077495925c17230E5e8951443d547ECdbB4925Bb#code)

We propose 181,850 Beans are minted to the following address in order to pay the bounty to the whitehat:
* [`0x8E9935504736e1716a9b64FE65791A59bb8B2A99`](https://etherscan.io/address/0x8E9935504736e1716a9b64FE65791A59bb8B2A99)

We propose 18,185 Beans are minted to the following address in order to pay the 10% fee to Immunefi:
* [`0x2BC5fFc5De1a83a9e4cDDfA138bAEd516D70414b`](https://etherscan.io/address/0x2BC5fFc5De1a83a9e4cDDfA138bAEd516D70414b)

