# BIR-19: Odd Token Wells

## Proposer

Beanstalk Immunefi Committee

## Summary

Reward 3,000 Beans to the whitehat that reported the issue where Wells with an odd number of reserves (for example, a 3 token Well) can no longer be used after one interaction post-deployment.

## Links

* [BIC Process](https://docs.bean.money/governance/beanstalk/bic-process)
* [Safe Transaction](https://app.safe.global/transactions/tx?safe=eth:0x879c8B99430F28C4d297BD479Cd43396b4aCF697&id=multisig_0x879c8B99430F28C4d297BD479Cd43396b4aCF697_0xc084500ae53087880d94d00bfc95d67f4cb23aad588531ef051e6e83109fe81b)
* [Etherscan Transaction](https://etherscan.io/tx/0xaebb350ef5de6d359f94b260bcb20cae0e001d0fe9d755954c196e403386a143)
* [Arweave](https://arweave.net/NMcEunsrgx-sPPMgBxGG-cCFTrWbo6vbbdSGKydZaWs)

## Bug

After deploying an odd reserve Well and a interacting with it once (i.e., adding liquidity), any future interactions with the Well will revert due to the memory storage implemented by `LibBytes.storeUint128()`  copying and saving the wrong token value.

## Fix

Update the `LibBytes.storeUint128()` function to properly store the reserves in the correct slots. Pull request [here](https://github.com/BeanstalkFarms/Basin/pull/136).

## Determination

No odd reserve Wells have ever been deployed nor are intended to be deployed in the near future. Given that the practicable economic damage is zero as a result of the low likelihood of this hypothetical situation occurring, the BIC has determined that this bug report be rewarded 3,000 Beans.

* Potential practicable economic damage: N/A
* Impact: Medium — _Contract fails to deliver promised returns, but doesn't lose value_
* Entitled to reward: Yes
