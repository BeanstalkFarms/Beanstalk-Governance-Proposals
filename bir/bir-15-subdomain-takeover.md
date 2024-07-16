# BIR-15: Subdomain Takeover

## Proposer

Beanstalk Immunefi Committee

## Summary

Reward 1,000 Beans to the whitehat that reported the issue that could result in takeover of phoenix.node.bean.money subdomain.

## Links

* [BIC Process](https://docs.bean.money/governance/beanstalk/bic-process)
* [Safe Transaction](https://app.safe.global/transactions/tx?safe=eth:0x879c8B99430F28C4d297BD479Cd43396b4aCF697&id=multisig_0x879c8B99430F28C4d297BD479Cd43396b4aCF697_0xf23e450d4ab994fbd95fd6ae1e002ed8639e3025404d0f82901690fcffcba8cf)
* [Etherscan Transaction](https://etherscan.io/tx/0x5f4b03a71568a5504df74cb586380c360cdcbbec087a5a88a619cffd21056375)

## Bug

The phoenix.node.bean.money subdomain was pointing to an unclaimed Google Cloud IP address, making it vulnerable to subdomain takeover. This would allow an attacker to potentially phish users by displaying malicious information and/or links at the subdomain.

## Fix

Remove phoenix.node.bean.money from the Cloudflare DNS settings.

## Determination

The BIC determined that the impact of this issue is low given that the phoenix.node.bean.money subdomain is not in use (it was an old RPC URL used by Beanstalk Farms for testing) and is easily mitigated. 

That said, all bean.money subdomain takeovers were in scope at the time the bug was reported (categorized as High impact). For these reasons, the BIC has determined that this bug report be rewarded 1,000 Beans.

* Potential practicable economic damage: N/A
* Impact: High — _Subdomain takeover other than app.bean.money_
* Entitled to reward: Yes
