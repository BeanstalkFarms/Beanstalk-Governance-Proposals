# BIR-21: L1 block.number

## Proposer

Beanstalk Immunefi Committee

## Summary

Reward 5,000 Beans to the whitehat that reported the L1 `block.number` issue resolved in [EBIP-19](https://arweave.net/ykNMX6ZzhuT_M4FaCbxPV3do87V3RPAYBoPxO09U5lI).

## Links

* [BIC Process](https://docs.bean.money/governance/beanstalk/bic-process)
* [Safe Transaction](https://app.safe.global/transactions/tx?safe=arb1:0x390b023d316c2e92dd96A9bcC7fAe8dB12A2fBC1&id=multisig_0x390b023d316c2e92dd96A9bcC7fAe8dB12A2fBC1_0x480a0e41342c588d544e70999e81e8a8a99c7c4cf69dbf3e4a8ef30660d0e214)
* [Arbiscan Transaction](https://arbiscan.io/tx/0x70df7393caf7c98aaea596728a74d97fb9e86740ff1f91c30bf628a8d57dcbd3)
* [Arbiscan](https://arweave.net/iq85yCgAd-S6W7p3yQGY1kDt58bCOuXN6Skh6_TCTgg)

## Bug

The Morning Auction and Generalized Convert used `block.number` to determine how much time had passed and Convert capacity (the amount of assets that can be Converted within 1 L2 block), respectively. On Arbitrum, `block.number` returns the L1 block number rather than the latest block number on Arbitrum. As a result, the Morning Auction had been lasting longer than its intended duration (the entire Season) and the Convert capacity was accrued over multiple blocks rather a single block.

## Fix

As implemented in EBIP-19, update the Morning Auction and Generalized Convert to fetch the L2 block number.

## Determination

Given that no funds were at risk and that the only consequences of the bug were (1) the Morning Auction had been lasting longer than its intended duration (the entire Season), and (2) the Convert capacity was accrued over multiple blocks rather a single block, the BIC has determined that this report be rewarded 5,000 Beans.

* Potential practicable economic damage: N/A
* Impact: Medium — _Contract fails to deliver promised returns, but doesn't lose value_
* Entitled to reward: Yes
