# BIP-46: Hypernative

Proposed: May 14, 2024

Status: Passed

Link: [Snapshot](https://snapshot.org/#/beanstalkdao.eth/proposal/0x76fcaac6c3e2f6982d54f528d1fcf8dd7e51b919a9e5ac032c9ca3a50da0700f)

---

- [Proposer](#proposer)
- [Summary](#summary)
- [Links](#links)
- [Context](#context)
- [Problem](#problem)
- [Proposed Solution](#proposed-solution)
- [Rationale](#rationale)
- [Contract Changes](#contract-changes)
- [Beans Minted](#beans-minted)
- [Effective](#effective)

## Proposer

Hypernative, Beanstalk Farms

Proposer Wallet: [0x9e0cb69ae6a5ad4eb870eb18d051efe642ed7db4](https://etherscan.io/verifySig/42406)

## Summary

* Mint 30,000 Beans to retain Hypernative, a proactive threat prevention and real-time monitoring platform, to protect Beanstalk from exploits and augment Beanstalk Farms' security operations thru May 2025;
* Add the Hypernative contract as a module to the Beanstalk Community Multisig (BCM) on Safe, allowing Hypernative to programmatically remove functions from Beanstalk upon detecting an impending or in-progress attack.

## Links

* [BIP-46 GitHub PR](https://github.com/BeanstalkFarms/Beanstalk/pull/842)
* [Safe Transaction](https://app.safe.global/transactions/tx?safe=eth:0xa9bA2C40b263843C04d344727b954A545c81D043&id=multisig_0xa9bA2C40b263843C04d344727b954A545c81D043_0xf46bf943dc5af605f6734f02f41bb2411365576753e2b317ec90d26738fc2f8f)

## Context

[Hypernative](https://hypernative.io/) actively detects and responds to zero-day cyber attacks, financial risks, on-chain anomalies to safeguard digital assets, protocols, and web3 applications from significant threats and losses. Hypernative works with some of the leading organizations in crypto such as Balancer, Polygon, Starknet, Zetachain, Linea (Consensys), Circle, Galaxy, OlympusDAO, Chainalysis and many others.

Hypernative is an active participant in many crypto security organizations and committees geared towards helping projects and the industry as a whole create new security solutions and standards, such as the [SEAL 911](https://github.com/security-alliance/seal-911) initiative by the [Security Alliance](https://securityalliance.org/).

The Hypernative team is well experienced in crypto and cyber security with tens of years of combined experience from companies like Microsoft, IBM, Google, VMware, CyberArk, ChainReaction, Orbs, Intel, and others.

Here are few mentions of Hypernative from protocols that were helped (some of which became customers):
* [Radiant Capital](https://twitter.com/RDNTCapital/status/1742638373863325790)
* [Telcoin](https://twitter.com/mwilliammyers/status/1745356262567739485)
* [DEUS](https://twitter.com/DeusDao/status/1661751727228596226)
* [Hundred Finance](https://blog.hundred.finance/15-04-23-hundred-finance-hack-post-mortem-d895b618cf33)
* [BonqDAO](https://mirror.xyz/bonqdaoblog.eth/Mq4qgNieUi-ytphYzPU-lWY_E1J2F7STq_xlCR3qGsE)
* [MahaDAO](https://twitter.com/senamakel/status/1610953131252416513)
* [Palmswap](https://twitter.com/Palmswaporg/status/1684902587303104512)
* [Jaypeggerz](https://twitter.com/jaypeggerz/status/1608395021031723010)
* [Valantis Labs](https://twitter.com/0xGreg_/status/1608418111887396864)
* [XaveFinance](https://twitter.com/XaveFinance/status/1579735814824931329)
* [Euler Finance](https://www.coinage.media/s2/he-stole-200-million-he-gave-it-back-now-hes-ready-to-explain-why)

And a few public use case stories from customers:
* [Liquid Collective](https://twitter.com/liquid_col/status/1726963707044143530)
* [Linea](https://twitter.com/HypernativeLabs/status/1755694828220735972)
* [Chainalysis](https://twitter.com/HypernativeLabs/status/1698982087246696634)
* [Idle DAO](https://twitter.com/idlefinance/status/1698697691222425685)
* [ZetaChain](https://twitter.com/zetablockchain/status/1675874912328712192)
* [Polygon](https://twitter.com/0xPolygonDevs/status/1672286493115596808)
* [3A DAO](https://twitter.com/HypernativeLabs/status/1725167225869332919)
* [Starknet](https://twitter.com/Starknet/status/1668222611950645250)
* [zkLend](https://twitter.com/zkLend/status/1650454560694239233)
* [Karpatkey](https://medium.com/@Hypernative/hypernative-partners-with-karpatkey-for-on-chain-security-and-risk-prevention-63e813335c2d)
* [Quantstamp](https://quantstamp.com/blog/quantstamp-x-hypernative-partner-to-enhance-web3-security)

## Problem

Security is paramount to Beanstalk's success. A critical component of a robust security stack that is not yet in place for Beanstalk is a real-time production monitoring solution.

It is challenging to keep track of various new security risks and exposures. Having a dedicated team and a real-time platform to detect and mitigate these risks for the Beanstalk community is high value.

It is impractical for the BCM to manually remove vulnerable functions from Beanstalk in a matter of minutes, which is often the amount of time between malicious contracts being deployed and exploits being executed.

## Proposed Solution

Hire Hypernative, and in doing so, add a [Safe module](https://docs.safe.global/advanced/smart-account-modules) to the BCM that grants Hypernative the ability to remove non-diamond functions from Beanstalk upon the detection of:
* High confidence pre-exploit detections; and
* High confidence exploit-in-progress detections.

In particular, Hypernative does not have the ability to remove the `diamondCut`, `facetAddress`, `facetAddresses`, `facetFunctionSelectors` and `facets` functions. The module contract that implements this functionality is non-upgradable ([address here](https://etherscan.io/address/0x59c78c1c2b4b03b4530d5f46f02362e4a03efe4d)).

Vulnerabilities that have the potential for theft of funds stem from incorrectly implemented functions that change state within Beanstalk. When Hypernative's platform detects an impending or in-progress attack, Beanstalk can be protected by simply removing all functions (in this case, all non-diamond functions in order to preserve upgradability), albeit at the cost of minor downtime to the system. Over the last year, Hypernative has detected 99.5% of all on-chain attacks and in 98% of cases detected them 2 or more minutes before the first transaction of the attack.

The BCM continues to be the owner of and the sole address capable of adding and updating functions on Beanstalk. Instances where Hypernative removes all non-diamond functions will be considered EBIPs, i.e., they will be documented as such and follow the DAO-approved procedures outlined in [Emergency Response Procedures](https://docs.bean.money/almanac/governance/beanstalk/bcm-process#emergency-response-procedures).

In addition, Hypernative also offers a security and Solidity expert that is available to provide expertise and assistance regarding security incidents, bug/vulnerability disclosures, etc. and over the last several months they have worked with Beanstalk Farms on integrating the automated response system into Beanstalk. The Hypernative platform also provides real-time detection and alerts to the community regarding anomalies and risks in governance proposals, oracles, participants, phishing and/or scamming campaigns affecting Beanstalk and its participants.

For reference, the following is a comprehensive list of features that Hypernative offers to customers:

> **A. Protocol Security**
> 
> 1. Security Framework and Response Procedure Review
> * Set standard operational procedure (response & contact points) on the category of events and time-sensitivity for any security or operational case;
> * Understand and create pre-incident measures to mitigate risk and react in time (pause contracts, limit/cap protocol, blacklist addresses, move funds to a safe/vault for emergency, etc.);
> * Understand and create post-incident measures; and
> * Automatically notify Chainalysis to label attacker wallets and track stolen funds.
> 
> 2. Protocol Security Alerts
> * Leverage Hypernative zero-day detection modules to detect threats and alerts in real-time on security incidents related to or directed at Beanstalk contracts.
> 
> 3. Preventive Workflows
> * Work with the Beanstalk Farms to connect critical security alerts from Hypernative platform into preventive actions agreed upon based on the security framework review; and
> * Provide consultancy and verification of the entire end-to-end real-time security process and connected alerts.
> 
> 4. Incident Response
> * Identify root cause(s) and suggest remedies/repairs and communication.
> 
> **B. Oracles, Bridges, and related Tokens**
> 
> 1. Oracle Reliability
> * Detect deviations between two updates of an oracle;
> * Detect deviations between two updates on two different chains;
> * Detect deviations between on-chain and off-chain prices; and
> * Detect a lack of updates and staleness.
> 
> 2. Bridge Security Monitoring
> * Provide security alerts related to bridge security incidents and risks.
> 
> 3. Related Token Monitoring
> * Monitor tokens dependent on or related to Beanstalk for anomalies, market economic conditions, security, holdings concentration, and supply changes (mints/burns).
> 
> **C. Phishing and Scamming Detection**
> 
> 1. On-chain detection
> * Detect phishing campaigns targeted at Beanstalk participants and provide alerts to warn the community.
> 
> **D. Participants Monitoring**
> 
> 1. Monitor suspicious users
> * Monitor large transfers or movements of funds from participants in the protocol; and
> * Monitor suspicious or illicit activity or illicit funds holdings for protocol participants.
> 
> 2. Monitor blacklisted addresses
> * Monitor addresses from OFAC lists or that were part of a hack/exploit/fraud.
> 
> **E. Protocol Operations Monitoring**
> 
> 1. Monitor protocol treasury and wallets
> * Monitor large transfers or movements of funds from protocol treasury;
> * Monitor protocol multi-sig wallets for anomalies and suspicious transactions; and
> * Pre-transaction API that can simulate a transaction outcome before applying it on-chain.
> 
> 2. Monitor protocol-defined parameters/invariants
> * Monitor specific invariants, functions, and events as specified by Beanstalk.

We propose to add the following disclosure to the [Beanstalk DAO Disclosures](https://arweave.net/zaOThrkaNCQ2zkkas8pyVQkFSkAKXfU7JahilBaqXRs):

> MOST BEANSTALK FUNCTIONS CAN BE ARBITRARILY REMOVED BY HYPERNATIVE, A PROACTIVE THREAT PREVENTION AND REAL-TIME MONITORING PLATFORM. THERE IS NO GUARANTEE THAT FUNCTIONS ARE ONLY REMOVED WHEN APPROPRIATE.
> 
> The Beanstalk DAO implemented Hypernative into Beanstalk, a proactive threat prevention and real-time monitoring platform. Hypernative has the ability to remove any Beanstalk function unrelated to the Ethereum Diamond and upgradability of Beanstalk. 
> 
> Hypernative introduces significant risks related to security and censorship. There is no guarantee that:
> * Hypernative only removes functions during high confidence pre-exploit and exploit-in-progress detections;
> * The BCM will remove Hypernative protections when necessary for the security or censorship resistance of Beanstalk; or that
> * The BCM does not remove Hypernative protections when it is not beneficial to Beanstalk.

## Rationale

Security is paramount to the success of Beanstalk. Hypernative has a strong track record and is the best in the industry at helping DAOs and protocols respond to exploit attempts in real-time.

By limiting the privileges of the Hypernative module to removing non-diamond functions, the only additional risk introduced is the potential for brief downtime (and in the event of downtime due to Hypernative malfunctioning, the BCM can remove Hypernative from Beanstalk at any time).

Paying the annual subscription of 30,000 Beans to Hypernative has the potential to significantly improve the security of Beanstalk, which currently custodies >$14M of non-Bean value.

## Contract Changes

There are no upgrades to the Beanstalk contract in this BIP.

The Safe module proposed to be added to the BCM Safe multisig is deployed at [0x59c78c1c2b4b03b4530d5f46f02362e4a03efe4d](https://etherscan.io/address/0x59c78c1c2b4b03b4530d5f46f02362e4a03efe4d). This module contract is owned by the EOA at [0xd7E13e49e467637D75C43D917d98d69049a19bFF](https://etherscan.io/address/0xd7E13e49e467637D75C43D917d98d69049a19bFF), which is custodied by Hypernative.

## Beans Minted

The `init` function on the `InitMint` contract at [0x077495925c17230E5e8951443d547ECdbB4925Bb](https://etherscan.io/address/0x077495925c17230E5e8951443d547ECdbB4925Bb#code) is called.

We propose a total of 30,000 Beans are minted to the Beanstalk Farms Multisig address upon the execution of this BIP. Beanstalk Farms will coordinate the payment to Hypernative.

## Effective

Immediately upon commitment.
