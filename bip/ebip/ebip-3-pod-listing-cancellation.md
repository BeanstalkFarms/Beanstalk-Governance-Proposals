# EBIP-3: Pod Listing Cancellation

Committed: October 25, 2022

---

- [Submitter](#submitter)
- [Emergency Process Note](#emergency-process-note)
- [Links](#links)
- [Problem](#problem)
- [Solution](#solution)
- [Contract Changes](#contract-changes)
- [Effective](#effective)

## Submitter

Beanstalk Community Multisig

## Emergency Process Note

Per the process outlined in the [BCM Emergency Response Procedures](https://docs.bean.money/governance/beanstalk/bcm-process#emergency-response-procedures), an emergency hotfix may be implemented by an emergency vote of the BCM if the bug is minor and does not require significant code changes.

This bug was reported by a whitehat on Immunefi.

## Links

- GitHub Commit Hash: [236be8fde5a24cf0189543fea14d5d946b1754f4](https://github.com/BeanstalkFarms/Beanstalk/commit/236be8fde5a24cf0189543fea14d5d946b1754f4)
- [Gnosis Transaction](https://gnosis-safe.io/app/eth:0xa9bA2C40b263843C04d344727b954A545c81D043/transactions/multisig_0xa9bA2C40b263843C04d344727b954A545c81D043_0xdac18161b1a78020e715360658d391ff442fed8ed43cd959516e4d05669f52e9)
- [Etherscan Transaction (TODO)](TODO)

## Problem

Farmers could cancel Pod Listings on behalf of Farmers by calling the `fillPodListing(...)` function with an input `beanAmount = 0`.

## Solution

Add the following check: `require(amount > 0, "Marketplace: Must fill > 0 Pods.");`

The fix has been reviewed by Halborn.

## **Contract Changes**

The following callable functions are modified in Beanstalk:

| Name             | Selector     | Facet              |
|:-----------------|:-------------|:-------------------|
| `fillPodListing` | `0x1aac9789` | `MarketplaceFacet` |

## Effective

Effective immediately upon commit by the BCM, which has already happened.
