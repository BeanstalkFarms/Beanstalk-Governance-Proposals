# BIR-6: Instantaneous ETH/USD Price

Proposed: October 30, 2023

Status: Passed

Link: [Snapshot](https://snapshot.org/#/beanstalkbugbounty.eth/proposal/0x7c620c229c051514562e270f583b915932563b4bd323f35b4287fb2ed2458513), [Arweave](https://arweave.net/Om0-LgRemqBfFd5KB32EggCCyNbBZitOCVJA5nLnQfA)

---

- [Proposer](#proposer)
- [Summary](#summary)
- [Links](#links)
- [Bug](#bug)
- [Fix](#fix)
- [Determination](#determination)
- [Beans Minted](#beans-minted)

## Proposer

Beanstalk Immunefi Committee

## Summary

* Mint 10,000 Beans to the whitehat that reported the issue fixed in [EBIP-11](https://arweave.net/9ZA_3YPeGyQVtS8wbDeN7MSTprGDzegq5iIewnAAxx4); and
* Mint 1,000 Beans to Immunefi's address in order to cover the 10% fee.

## Links

* [BIC Process](https://docs.bean.money/governance/beanstalk/bic-process)
* [Safe Transaction](https://app.safe.global/transactions/tx?safe=eth:0xa9bA2C40b263843C04d344727b954A545c81D043&id=multisig_0xa9bA2C40b263843C04d344727b954A545c81D043_0x3246f5fb18d6106bc5ccdbaf7c4f242a5827dc81aeb1c7708e425a76dae62937)
* [EBIP-11: Upgrade Minting Oracle to 60 Minute TWA ETH/USD Price](https://arweave.net/9ZA_3YPeGyQVtS8wbDeN7MSTprGDzegq5iIewnAAxx4)

## Bug

When determining how many Beans/Soil to mint, Beanstalk calculated the price of ETH in USD using the instantaneous price from the Chainlink ETH/USD data feed and compared it 15 minute TWA prices in the ETH:USDC and ETH:USDT 0.05% fee Uniswap V3 pools. 

When minting Beans during a `gm` call, Beanstalk compared this USD price of ETH with the TWA reserves of Beans and ETH in Multi Flow to calculate the TWA deltaB in the BEANETH Well. Because the Chainlink price in the former was not time weighted, the TWA deltaB calculated at the end of the Season would be higher than necessary if the ETH price was increasing. Similarly, the TWA deltaB calculated at the end of the Season would be lower than necessary if the ETH price was decreasing.

## Fix

Add support for querying a time weighted average (TWA) ETH/USD price from Chainlink.

Upgrade the ETH/USD price that the Well minting oracle uses to:
* 60 minute TWA price from the ETH/USD Chainlink data feed; and
* 60 minute TWA prices from the ETH:USDC and ETH:USDT 0.05% fee Uniswap V3 pools. 

This was fixed in EBIP-11.

## Determination

Based on the Immunefi Bug Bounty Program, this submission's reward is capped at the lower of (a) 10% of practicable economic damage, or (b) 10,000 Beans. Bug reports that do not come with a PoC and code implementing a fix may qualify for a maximum of up to 30% of said reward.

The BIC determined that it is not possible to calculate the funds at risk or practicable economic damage for this issue given that it is not exploitable by a malicious actor and is only realized via ETH price changes.

However, despite the issue not being exploitable and the report not including code implementing a fix, the BIC has determined that this particular bug report be rewarded 10,000 Beans given the significance of the issue.

* Potential practicable economic damage: N/A
* Impact: High (_Illegitimate minting of protocol native assets_)
* Entitled to reward: Yes

## Beans Minted

The `init` function on the following `InitMint` contract is called:
* [`0x077495925c17230E5e8951443d547ECdbB4925Bb`](https://etherscan.io/address/0x077495925c17230E5e8951443d547ECdbB4925Bb#code)

We propose 10,000 Beans are minted to the following address in order to pay the bounty to the whitehat:
* [`0x2b816A1afb2CFA9Fb13e3f4d5487f0689187FBdB`](https://etherscan.io/address/0x2b816A1afb2CFA9Fb13e3f4d5487f0689187FBdB)

We propose 1,000 Beans are minted to the following address in order to pay the 10% fee to Immunefi:
* [`0x2BC5fFc5De1a83a9e4cDDfA138bAEd516D70414b`](https://etherscan.io/address/0x2BC5fFc5De1a83a9e4cDDfA138bAEd516D70414b)
