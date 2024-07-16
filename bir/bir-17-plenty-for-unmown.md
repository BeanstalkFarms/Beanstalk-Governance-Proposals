# BIR-17: Plenty for Unmown Germinating Deposits

## Proposer

Beanstalk Immunefi Committee

## Summary

Reward 10,000 Beans to the whitehat that reported the issue where Deposits that have completed Germination but have not been Mown yet do not earn Plenty.

## Links

* [BIC Process](https://docs.bean.money/governance/beanstalk/bic-process)
* [Safe Transaction](https://app.safe.global/transactions/tx?safe=eth:0x879c8B99430F28C4d297BD479Cd43396b4aCF697&id=multisig_0x879c8B99430F28C4d297BD479Cd43396b4aCF697_0x79153efa13bad48ccd13f0a679935997b97b3bb21635dff53b1a341e17ce5cf4)
* [Etherscan Transaction](https://etherscan.io/tx/0x90b57345c209fe7867f66e51130d35233cffd2ed0095ce5fd15c602dec108b7d)

## Bug

Currently, when Plenty is distributed to Roots (an internal accounting variable that tracks Stalk ownership) based on the Roots at the time of the Flood, Roots associated with Deposits that have completed Germination but have not been Mown yet are not included.

## Fix

In `LibSilo._mow()`, perform the Germination process for the Farmer prior to performing logic related to Flood.

## Determination

The Pod Rate must be less than 5% in order for Flood to occur, is currently over 1800%, and has not been lower than 5% since September 2021. As a result, it is unlikely for this issue to surface given the current state of Beanstalk. However, "Permanent freezing of unclaimed yield" describes the issue well, resulting in a High severity report. 

Given that the practicable economic damage is near zero as a result of the low likelihood of Flood occurring, the BIC has determined that this bug report be rewarded the minimum reward for High severity reports of 10k Beans.

* Potential practicable economic damage: N/A
* Impact: High — _Permanent freezing of unclaimed yield_
* Entitled to reward: Yes
