# BFBP-C-13: Fund Codehawks Audit for Beanstalk 3

Proposed: May 29, 2024

Status: Passed

Link: [Snapshot](https://snapshot.org/#/beanstalkfarmsbudget.eth/proposal/0x6cd3137e530bafc7c923b24ff2c79a9367391133df584386210da46f5a235e8e), [Arweave](https://arweave.net/0qobapPD8Nei6QoF7F3oWWhDJMePLBX79zw-7U_c9As)

---

- [Proposer](#proposer)
- [Summary](#summary)
- [Proposal](#proposal)
- [Additional Notes](#additional-notes)

## Proposer

Guy

## Summary

Fund a [Codehawks](https://www.codehawks.com/) competitive audit for Beanstalk 3 with 275,000 Beans, which includes implementations of (at least):
* [RFC: Generalized Convert](https://github.com/BeanstalkFarms/Beanstalk/issues/716);
* [RFC: Generalized Flood](https://github.com/BeanstalkFarms/Beanstalk/issues/740);
* [RFC: Tractor](https://github.com/BeanstalkFarms/Beanstalk/issues/734);
* [RFC: Secure Beanstalk](https://github.com/BeanstalkFarms/Beanstalk/issues/729), which includes:
    * Upgrading the test suite to support Foundry;
    * Upgrading the Solidity version of Beanstalk from 0.7.6 to 0.8.20;
    * Upgrading Beanstalk to Storage V2;
    * Migrating constants to state that are likely to be adjusted by the DAO in the future;
* Whitelist v1.1 (discussed later);
* Field V2 (discussed later); and
* A series of contracts that can be used to migrate Beanstalk's state to an Ethereum L2.

## Proposal

[Codehawks](https://www.codehawks.com/) is a competitive audit platform facilitated by [Cyfrin](https://cyfrin.io/). The past few Codehawks audits (See [1](https://www.codehawks.com/report/clsxlpte900074r5et7x6kh96), [2](https://www.codehawks.com/report/clu7665bs0001fmt5yahc8tyh), [3](https://www.codehawks.com/report/clv1eptuo0003bcnzce1ap7om), [4](https://www.codehawks.com/contests/clvo5kwin00078k6jhhjobn22)) have shown that having judges with context on the protocol (as Cyfrin does) is a significant value add for determining the validity and severity of reports.

The prize pool for the Beanstalk 3 audit will be $250,000, with a 10% platform fee of $25,000 for a total cost of $275,000. Given the Beanstalk 3 upgrade (particularly Secure Beanstalk) touches many parts of the Beanstalk codebase, it is prudent to include the entire codebase as in scope of the audit.

The Beanstalk 3 audit competition is scheduled start on Thursday, May 30, 2024 and end ~5.5 weeks later on Monday, July 8, 2024.

## Additional Notes

### Foundry

Beanstalk currently uses Hardhat as its main testing suite, which lacks many great features for testing such as:
* Fuzz testing;
* Differential testing;
* `ffi` scripts; and
* Stack traces.

A significant number of whitehats and auditors prefer Foundry over Hardhat due to the tools available to them. 

Hardhat testing is slow to compile (~1 minute to compile, ~5 minutes to run the test suite, ~5-10 minutes to run a stack trace, etc.) compared to Foundry testing. This leads to inefficient use of developer time.

Thus, we propose to migrate all tests from Hardhat to Foundry.

### Solidity 0.8.20

Beanstalk uses Solidity version 0.7.6. As a result, many QoL tools are not available to the Beanstalk development community, such as:
* Using additional methods provided in EIP-1559;
* Naming variables from mappings; and
* Using SafeMath by default (leading to overflow).

Most DeFi protocols use Solidity v0.8 or higher. Upgrading Solidity versions allows the Beanstalk development community to take advantage of tooling that is developed with v0.8.0^ in mind. For example, using Foundry with v0.7.6 requires a number of workarounds in order to properly use it.

Furthermore, whitehats and auditors are generally more familiar with Solidity v0.8.0 and higher.

Thus, we propose to upgrade all contracts to v0.8.20. While there are more recent versions, Beanstalk should strike a balance between using a battle tested compiler and the latest version. For reference, Basin is developed in v0.8.20.

### Storage V2

Beanstalk uses diamond storage to read and write variables. With the introduction of Tractor, there are storage slots for the diamond implementation, Tractor, and a general storage slot. The slots for these are currently:

```solidity
ds.slot = DIAMOND_STORAGE_POSITION // keccak256("diamond.standard.diamond.storage");
s.slot = 0;
t.slot = TRACTOR_STORAGE_POSITION // keccak256("diamond.storage.tractor"); 
```

This makes it incredibly difficult to implement changes in Beanstalk storage, compromising security, and generally increasing development timelines.

Thus, we propose to organize Beanstalk data into categories and store them separately, and unpack a majority of variables to reduce security risk.

### Whitelist v1.1

Whitelisting a Well requires code changes to the Beanstalk contracts. While the changes can potentially be minimal, they ultimately increase the friction associated with whitelisting a Well. Ideally, the DAO can whitelist a Well with the `init` function of a diamond cut. 

Thus, we propose to implement a generalized oracle, liquidity weight function and Gauge Point function.

### Field V2

Beanstalk cannot easily add, modify or remove Yield Distributors, i.e., places where new Bean mints are allocated (currently the Silo, Field and Barn). It also cannot easily change the distribution of new Bean mints between various Yield Distributors.

Beanstalk cannot support multiple Pod Lines, it cannot easily add, modify or remove Pod Lines and it cannot control the issuance of debt to particular Pod Lines.

Thus, we propose a generalized yield distribution system and Pod Line system. For reference, see [Temp-Check-4](https://snapshot.org/#/beanstalkfarms.eth/proposal/0xc716cb01aeecc01ea4127ace7219e7efe644e8173d228c6b6ff9331c4d373222).

### L2 Migration

Beanstalk is a fairly gaseous protocol. While the costs of interacting with Beanstalk at the time of writing in May 2024 are reasonable, activity on Ethereum L1 is currently low. In the past when Gwei has reached mid to high double digit values, interacting with Beanstalk (such as Mowing, Planting, or Converting) have cost upwards of several hundred US dollars. This prices out smaller Farmers and reduces the efficacy of Beanstalk's peg maintenance mechanisms. 

Thus, we propose a series of initialization scripts that migrate Beanstalk state to an L2 and a facet to allow smart contract addresses to choose an address on the L2 to claim their assets with. For reference, see [Temp-Check-5](https://snapshot.org/#/beanstalkfarms.eth/proposal/0x93dfc538a66c1c199f5c9f0fd9c0233ce3625c7ada9743bafc8b5fbc0fc38fc7).
