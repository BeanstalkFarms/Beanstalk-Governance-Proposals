# BIR-20: Micro BDV Stays with Sender

## Proposer

Beanstalk Immunefi Committee

## Summary

Reward 3,000 Beans to the whitehat that reported the issue where a micro amount of BDV stays with the sender of a Deposit upon Transfer.

## Links

* [BIC Process](https://docs.bean.money/governance/beanstalk/bic-process)
* [Safe Transaction](https://app.safe.global/transactions/tx?safe=eth:0x879c8B99430F28C4d297BD479Cd43396b4aCF697&id=multisig_0x879c8B99430F28C4d297BD479Cd43396b4aCF697_0x89f9b0fd5933d7bd2d5b615e03325debe355161b5d6444e42784c74b3e1a39c0)
* [Etherscan Transaction](https://etherscan.io/tx/0xe1c96785e2475b6c9ae79665be81a69fb9c693b99fded8958d000a718d1cdd94)
* [Arweave](https://arweave.net/AJeMZ-XbvTTv_WssrmoTzhmsKfQEMMJOrow1Z2sck-M)

## Bug

During a Deposit transfer, a micro amount of BDV stays with the sender due to a rounding error in `LibTokenSilo.removeDepositFromAccount()`.

## Fix

Update `LibTokenSilo.removeDepositFromAccount()` to properly round up the result of integer division when calculating the removed BDV from the sender.

## Determination

Given that the micro BDV that stays with the sender is not transferred to the receiver, no additional BDV is issued and no Deposits are put into an incorrect state. Given that the practicable economic damage is effectively zero as a result of the magnitude of the issue, the BIC has determined that this bug report be rewarded 3,000 Beans.

* Potential practicable economic damage: N/A
* Impact: Medium — _Contract fails to deliver promised returns, but doesn't lose value_
* Entitled to reward: Yes
