# BIP-47: Adjust Quorum

Proposed: May 22, 2024

Status: Passed

Link: [Snapshot](https://snapshot.org/#/beanstalkdao.eth/proposal/0x4b1a7b2e775dde2c6244d4a0a64fe4221814a400280e65d8049ab7885bf788d2)

---

- [Proposer](#proposer)
- [Summary](#summary)
- [Links](#links)
- [Problem](#problem)
- [Proposed Solution](#proposed-solution)
- [Rationale](#rationale)
- [Contract Changes](#contract-changes)
- [Beans Minted](#beans-minted)
- [Effective](#effective)

## Proposer

Beanstalk Farms, Ben Weintraub, Brendan Sanderson

Proposer Wallet: [0x9e0cb69ae6a5ad4eb870eb18d051efe642ed7db4](https://etherscan.io/verifySig/42407)

## Summary

* Change quorum for BIPs from 50% of Stalk voting For to the minimum of (1) 50% of Stalk voting For and (2) one-third of Stalk, plus the amount of Stalk voting Against, voting For (and remove the Abstain voting choice from BIPs);
* Change quorum for BOPs from 35% of Stalk voting For (and the majority of participating Stalk voting For) to the minimum of (1) 50% of Stalk voting For and (2) 25% of Stalk, plus the amount of Stalk voting Against, voting For.

## Links

* [BIP-47 GitHub PR](https://github.com/BeanstalkFarms/Beanstalk/pull/866)

## Problem

Beanstalk is likely not at a point where it can sustain itself in perpetuity without additional development. If Beanstalk is to remain competitive in the open source environment that it operates in, improvements are essential.

It is becoming increasingly unlikely for any BIP to pass, as indicated by [BIP-44](https://arweave.net/zYTWn7E74vRLSeRrqGrE7TbPVEYqQAiCpIF2uvvzyA0), a proposal for which discussion started many months ago with minimal fundamental disagreement amongst the Beanstalk community (see the *#rfc-seed-gauge* channel in Discord, the [Beanstalk State Space](https://bean.money/beanstalk-state-space.pdf) recordings, etc.). Furthermore, the Beanstalk DAO formally signaled support for implementing the [Seed Gauge System RFC](https://github.com/BeanstalkFarms/Beanstalk/issues/726) in [BIP-40](https://arweave.net/fubHcxE_P_z-xBwapMUMLiT29m6_sifDtAKimQ05Nz8), the 2024 Beanstalk Farms development budget.

Given that most recent Stalk supply growth is due to Grown Stalk issuance rather than new Deposits, it seems that fewer existing Stalkholders are participating in governance.

Governance problems must be addressed proactively as waiting until BIPs can no longer pass would render Beanstalk effectively non-upgradable. 

## Proposed Solution

We propose to change quorum for BIPs from 50% of Stalk voting For to the minimum of (1) 50% of Stalk voting For and (2) one-third of Stalk, plus the amount of Stalk voting Against, voting For. The Abstain voting choice is removed.

Additionally, we propose to change quorum for BOPs from 35% of Stalk voting For (and the majority of participating Stalk voting For) to the minimum of (1) 50% of Stalk voting For and (2) 25% of Stalk, plus the amount of Stalk voting Against, voting For.

Note that there are no proposed changes to counting votes. In BIPs and BOPs, a Stalkholder's vote for a given proposal is counted as their Stalk (plus delegated Stalk) at the beginning of the Voting Period that still exists.

## Rationale

In the near term, upgradability is paramount to the success of Beanstalk. This proposed governance system increases the likelihood Beanstalk does not enter a circumstance where it cannot be upgraded despite there being no opposition to an upgrade.

## Contract Changes

None.

## Beans Minted

None.

## Effective

Immediately upon passage.
