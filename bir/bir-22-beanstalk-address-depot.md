# BIR-22: Beanstalk Address in Depot

## Proposer

Beanstalk Immunefi Committee

## Summary

Reward 1,000 Beans to the whitehat that reported that the standalone Depot contract used the L1 Beanstalk contract address.

## Links

* [BIC Process](https://docs.bean.money/governance/beanstalk/bic-process)
* [Safe Transaction](https://app.safe.global/transactions/tx?safe=arb1:0x390b023d316c2e92dd96A9bcC7fAe8dB12A2fBC1&id=multisig_0x390b023d316c2e92dd96A9bcC7fAe8dB12A2fBC1_0x480a0e41342c588d544e70999e81e8a8a99c7c4cf69dbf3e4a8ef30660d0e214)
* [Arbiscan Transaction](https://arbiscan.io/tx/0x70df7393caf7c98aaea596728a74d97fb9e86740ff1f91c30bf628a8d57dcbd3)
* [Arweave](https://arweave.net/TdrTxJk7NKPCJNboCejXnOUkiFhvlPiltzj_TwU_1m4)

## Bug

Any transactions interacting with the Depot contract on Arbitrum would revert as a result of Depot using the incorrect Arbitrum Beanstalk contract address.

Previous Depot contract: https://arbiscan.io/address/0xDEb0f0dEEc1A29ab97ABf65E537452D1B00A619c

## Fix

Deploy a new Depot contract with the correct [Arbitrum Beanstalk contract address](https://arbiscan.io/address/0xD1A0060ba708BC4BCD3DA6C37EFa8deDF015FB70).

New Depot contract: https://arbiscan.io/address/0xdeb0f082ed3b0efe9257aea9f2e6e974aa4120c3

## Determination

Given that no funds were ever at risk, the standalone Depot contract is not currently used anywhere and interacting with the contract would have simply reverted, the BIC has determined that this bug report be rewarded 1,000 Beans.

* Potential practicable economic damage: N/A
* Impact: Medium — _Contract fails to deliver promised returns, but doesn't lose value_
* Entitled to reward: Yes
