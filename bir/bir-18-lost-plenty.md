# BIR-18: Lost Plenty Edge Case

## Proposer

Beanstalk Immunefi Committee

## Summary

Reward 10,000 Beans to the whitehat that reported the issue where Farmers that earn Plenty from a Flood Withdraw all of their assets and then Mow during the next Oversaturation Season (referred to as `raining` in the contracts), lose Plenty associated with that Flood.

## Links

* [BIC Process](https://docs.bean.money/governance/beanstalk/bic-process)
* [Safe Transaction](https://app.safe.global/transactions/tx?safe=eth:0x879c8B99430F28C4d297BD479Cd43396b4aCF697&id=multisig_0x879c8B99430F28C4d297BD479Cd43396b4aCF697_0x488709f6c5f29d5085ecf7b852a6205793b97daf4c92445758cc6256ba72c481)
* [Etherscan Transaction](https://etherscan.io/tx/0xc892c81a7458a3480592a00ac9a7677a09a0fc0e3dbf4ce8e84f91daa3229079)
* [Arweave](https://arweave.net/qmprTnP-8vB-gc9eF6exJhqO0vl23ue5plNr5cK0WAU)

## Bug

If a Farmer earns Plenty from a Flood, Withdraws all of their assets, and then Mows during the next Oversaturation Season, they lose the Plenty associated with that Flood.

## Fix

Update `LibSilo._mow()` to allocate Plenty from the last Flood to the Farmer if applicable when `lastRain > 0`.

## Determination

The Pod Rate must be less than 5% in order for Flood to occur, is currently over 1800%, and has not been lower than 5% since September 2021. As a result, it is unlikely for this issue to surface given the current state of Beanstalk. However, "Permanent freezing of unclaimed yield" describes the issue well, resulting in a High severity report. 

Given that the practicable economic damage is near zero as a result of the low likelihood of Flood occurring, the BIC has determined that this bug report be rewarded the minimum reward for High severity reports of 10k Beans.

* Potential practicable economic damage: N/A
* Impact: High — _Permanent freezing of unclaimed yield_
* Entitled to reward: Yes
