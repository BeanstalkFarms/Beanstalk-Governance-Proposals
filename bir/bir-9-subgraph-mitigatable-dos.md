# BIR-9: Beanstalk Subgraph Mitigatable DoS

Proposed: January 2, 2024

Status: Passed

Link: [Snapshot](https://snapshot.org/#/beanstalkbugbounty.eth/proposal/0x3a6ce826f65fc198565a6d35852f21cde955141741052ad34e2f15d375820e12)

---

- [Proposer](#proposer)
- [Summary](#summary)
- [Links](#links)
- [Bug](#bug)
- [Determination](#determination)
- [Beans Minted](#beans-minted)

## Proposer

Beanstalk Immunefi Committee

## Summary

* Mint 1,000 Beans to the whitehat that reported the mitigatable DoS (Denial of Service) issue in the Beanstalk Subgraph; and
* Mint 100 Beans to Immunefi's address in order to cover the 10% fee.

## Links

* [BIC Process](https://docs.bean.money/governance/beanstalk/bic-process)
* [Safe Transaction](https://app.safe.global/transactions/tx?safe=eth:0xa9bA2C40b263843C04d344727b954A545c81D043&id=multisig_0xa9bA2C40b263843C04d344727b954A545c81D043_0xa0ec2733c90eab002923bb13615976d06345da54923daa97ff92860f99df0cc9)

## Bug

An attacker can DoS attack the GraphQL endpoint that serves the Beanstalk Subgraph. DoS attacks on centralized infrastructure (the subgraph is currently hosted on a Hetzner server) are not entirely mitigatable, but the current cloud server settings can be improved to mitigate this issue. A DoS attack on the subgraph would render parts of the UI temporarily unusable.

## Determination

Based on the bug bounty program, this submission's ( Website and Applications - High ) reward is based on a set of internal criteria established by the BIC (with a minimum reward of USD 1 000), primarily taking into account the exploitability of the bug, the impact it causes and likelihood of the vulnerability presenting itself.

The BIC determined that the impact of this issue is low given the minimal temporary downtime that would be caused by an attack. The report also describes a DDoS attack on the Beanstalk subgraph, not the UI hosted at [app.bean.money](https://app.bean.money/), which can partially function without the subgraph. 

Given this, the BIC has determined that this report qualifies for a reward of 1,000 Beans. 

* Potential practicable economic damage: N/A
* Impact: High _(A temporary or self-correcting loss of website availability, i.e., a mitigatable vulnerability to DDoS)_
* Entitled to reward: Yes

## Beans Minted

The `init` function on the following `InitMint` contract is called:
* [`0x077495925c17230E5e8951443d547ECdbB4925Bb`](https://etherscan.io/address/0x077495925c17230E5e8951443d547ECdbB4925Bb#code)

We propose 1,000 Beans are minted to the following address in order to pay the bounty to the whitehat:
* [`0x38bce8c1ca5a061aa8e2b4750c18ba3efb27ced9`](https://etherscan.io/address/0x38bce8c1ca5a061aa8e2b4750c18ba3efb27ced9)

We propose 100 Beans are minted to the following address in order to pay the 10% fee to Immunefi:
* [`0x2BC5fFc5De1a83a9e4cDDfA138bAEd516D70414b`](https://etherscan.io/address/0x2BC5fFc5De1a83a9e4cDDfA138bAEd516D70414b)
