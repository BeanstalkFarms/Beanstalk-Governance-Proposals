# BIR-14: Decrease Recapitalization Upon Chop

## Proposer

Beanstalk Immunefi Committee

## Summary

Mint 10,000 Beans to the whitehat that reported the issue that Chopping Unripe LP does not decrease the recapitalization target.

## Links

* [BIC Process](https://docs.bean.money/governance/beanstalk/bic-process)
* [Safe Transaction](https://app.safe.global/transactions/tx?safe=eth:0x879c8B99430F28C4d297BD479Cd43396b4aCF697&id=multisig_0x879c8B99430F28C4d297BD479Cd43396b4aCF697_0x14e76050070d38674c2c3b1118364e021edb9e147f51a359766e41c86769d5db)

## Bug

Currently, when Unripe LP holders Chop, `s.recapitalized` is unchanged. As a result, the Barn Raise ends earlier than intended, i.e., Beanstalk will not sell enough Fertilizer to fully recapitalize all Unripe LP.

## Fix

Decrement `s.recapitalized` by an amount proportional to the underlying Ripe assets removed upon Chop.

Given that the issue is low impact and not realized until the end of the Barn Raise, the BCM determined that an EBIP is not necessary. The goal is to include this fix in an upcoming BIP.

## Determination

Given that the bug is not exploitable and only results in returning less value than intended to users at the end of the Barn Raise, the most accurate impact in scope is "Contract fails to deliver promised returns, but doesn't lose value", i.e., Medium severity.

Although the impact is low, given the high likelihood of the vulnerability presenting itself later in the Barn Raise, the BIC has determined that this bug report be rewarded 10,000 Beans.

* Potential practicable economic damage: N/A
* Impact: Medium — _Contract fails to deliver promised returns, but doesn't lose value_
* Entitled to reward: Yes

## Beans Minted

The `init` function on the following `InitMint` contract is called:
* [`0x077495925c17230E5e8951443d547ECdbB4925Bb`](https://etherscan.io/address/0x077495925c17230E5e8951443d547ECdbB4925Bb#code)

We propose 10,000 Beans are minted to the following address in order to pay the bounty to the whitehat:
* [`0x55E44232BE66f69D7e116F8b7f8d07755b2cE7e5`](https://etherscan.io/address/0x55E44232BE66f69D7e116F8b7f8d07755b2cE7e5)
