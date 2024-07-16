# BIR-18: Lost Plenty Edge Case

## Proposer

Beanstalk Immunefi Committee

## Summary

Reward 10,000 Beans to the whitehat that reported the issue where Farmers that earn Plenty from a Flood Withdraw all of their assets and then Mow during the next Oversaturation Season (referred to as `raining` in the contracts), lose Plenty associated with that Flood.

## Links

* [BIC Process](https://docs.bean.money/governance/beanstalk/bic-process)
* [Safe Transaction TBD](TBD)
* [Etherscan Transaction TBD](TBD)

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
