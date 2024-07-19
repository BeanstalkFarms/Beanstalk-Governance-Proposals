# BIR-16: Plot Deletion

## Proposer

Beanstalk Immunefi Committee

## Summary

Reward 10,000 Beans to the whitehat that reported the issue where Plots can be deleted for a user who has an open Pod Order with a `minFillAmount` of 0.

## Links

* [BIC Process](https://docs.bean.money/governance/beanstalk/bic-process)
* [Safe Transaction](https://app.safe.global/transactions/tx?safe=eth:0x879c8B99430F28C4d297BD479Cd43396b4aCF697&id=multisig_0x879c8B99430F28C4d297BD479Cd43396b4aCF697_0xa14baa12a5812414b826769cfbd8c382fd320e573dd207f0b16bca375393d644)
* [Etherscan Transaction](https://etherscan.io/tx/0x7edbf455880c8d932600893c6a577e6529bc8647453a8f7e16a1ec73b70420e3)
* [Arweave](https://arweave.net/s13-Tri7UQCUSilW5-ojVfl9I6eHwVhmCIWbIa3TjCc)

## Bug

If a Farmer created a Pod Order with a `minFillAmount` of 0 and a `maxPlaceInLine` such that they have some Pods that are before this place in line (e.g., a Farmer creates an Order with a `maxPlaceInLine` of 50 million, and has a Plot at place 40 million in line), an attacker can fill this Pod Order (with a `minFillAmount` of 0) and delete the Farmer's Plot (by setting `index` to the index that the Farmer has). 

## Fix

Add a `minFillAmount > 0` check to `_createPodOrder` and `_createPodOrderV2`, such that future Pod Orders cannot be created with a zero `minFillAmount`. 

Add an `amount > 0` check to `_fillPodOrder` and `_fillPodOrderV2` to prevent existing Pod Orders from being executed with a zero `amount`. 

## Determination

At the time of report submission, there were 2 open Pod Orders with a `minFillAmount` of 0. One of the Pod Orders was not vulnerable because the Farmer did not have any Plots before `maxPlaceInLine`. However, the Farmer with the other Pod Order had about 80,000 Pods that were at risk.

The most accurate impact in scope to describe this issue would be Griefing (e.g. no profit motive for an attacker, but damage to the users or the protocol) because the attacker has nothing to gain by doing this, and any "attack" could be reversed by the Beanstalk Community Multisig via EBIP.

However, given the exploitability of the issue, the BIC has determined that this bug report be rewarded the maximum reward for Medium severity reports of 10,000 Beans.

* Potential practicable economic damage: N/A
* Impact: Medium — _Griefing (e.g. no profit motive for an attacker, but damage to the users or the protocol)_
* Entitled to reward: Yes
