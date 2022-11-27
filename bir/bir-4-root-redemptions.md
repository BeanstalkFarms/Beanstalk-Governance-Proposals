# BIR-4: Root Token Redemptions 

Proposed: November 23, 2022

Status: Passed

Link: [Snapshot](https://snapshot.org/#/beanstalkbugbounty.eth/proposal/0x60f6fcf25c3fe76003535708d9b14396dace659fddb2d6c7076da8ecce84840e), [Arweave](https://arweave.net/OyqpqjsTub1j4jaGipqMffPGxVywnmR3g45e9jkMZTM)

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

* [Safe Transaction](https://app.safe.global/eth:0xa9bA2C40b263843C04d344727b954A545c81D043/transactions/tx?id=multisig_0xa9bA2C40b263843C04d344727b954A545c81D043_0x5cbe4141ae9a46cba144443d1f1b1c1e1ac348ea2946e3c51a9e46c5a9609883)
* [ERIP-0: Redeem Fix](https://arweave.net/7fjU88EiBrvbLK8oW_GRld4DsSxYfm0yLVvm0x7ywnM)

## Bug

According to Section 3.3 of the [Root whitepaper](https://roottoken.org/root.pdf#subsection.3.3), the amount of Roots to be Redeemed from a set of Deposits  is derived from the maximum percentage change in the BDV, Stalk and Seeds of Root as a result of the Redemption.

However, in the Root code, the Roots to Redeem used the mininum instead of the maximum which allowed the user to receive more Bean Deposits than they were supposed to when Redeeming. 

## Fix

Update the `_transferDeposits()` function to subtract the minimum amount remaining from the supply in accordance with Section 3.3 of the [Root whitepaper](https://roottoken.org/root.pdf#subsection.3.3). Because subtraction occurs in the `_transferDeposits()` function, subtracting the maximum amount remaining from the supply resulted in the minimum of the change in BDV, Stalk and Seeds per Root required to Redeem being used instead of the maximum.

This was fixed in [ERIP-0](https://arweave.net/7fjU88EiBrvbLK8oW_GRld4DsSxYfm0yLVvm0x7ywnM).

## Determination

Although the Root token was not previously defined as in-scope, the BIC has decided due to the combination of the following reasons to offer a bounty for discovery of the bug and formally include the Root token contract in the Immunefi bug bounty program moving forward:

* The BIC had already determined to include the contract in the bug bounty program, but had not formalized it;
* The contract was audited by Halborn; and
* The contract has already started to attract a significant amount of Beans/BDV. 

As a result of this vulnerability, Root holders were able to Redeem for Bean Deposits with fewer Roots than they otherwise would. However, the scale at which this vulnerability could manifest itself was marginal. About ~8,500 Roots had been Redeemed across 12 transactions and an additional ~226 Beans were received from those Redemptions than expected.

Given that:
* It would require time and significant capital to steal any meaningful portion of the underlying BDV of Root;
* The current underlying BDV of Root is about 165k;
* Extrapolating the loss of Beans in past Redemptions due to this vulnerability to the current underlying BDV results in `(165,000 * 226) / 8500 = ~4387` Beans; and
* The minimum reward for High Impact (_Theft of unclaimed yield_) is 10,000 Beans:

The BIC determined that this bug report be rewarded 10,000 Beans.

* Potential practicable economic damage: ~$4,387
* Impact: High (_Theft of unclaimed yield_)
* Entitled to reward: Yes

## Beans Minted

The `init` function on the following `InitMint` contract is called:
* [`0x077495925c17230E5e8951443d547ECdbB4925Bb`](https://etherscan.io/address/0x077495925c17230E5e8951443d547ECdbB4925Bb#code)

We propose 10,000 Beans are minted to the following address in order to pay the bounty to the whitehat:
* [`0x39fAEF970Fba6822cC99f0270b32170Fe85B792e`](https://etherscan.io/address/0x39fAEF970Fba6822cC99f0270b32170Fe85B792e)

We propose 1,000 Beans are minted to the following address in order to pay the 10% fee to Immunefi:
* [`0x2BC5fFc5De1a83a9e4cDDfA138bAEd516D70414b`](https://etherscan.io/address/0x2BC5fFc5De1a83a9e4cDDfA138bAEd516D70414b)
