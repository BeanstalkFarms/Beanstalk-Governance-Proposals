# EBIP-18: Burn Excess Unripe and Ripe Beans

## Submitter

Beanstalk Community Multisig

## Summary

"Burn" the ~11.29m Unripe Beans and their associated ~3.1M Ripe Beans that were erroneously minted at Replant. In practice, this means not minting them on Arbitrum during the L2 migration process in [BIP-50](https://github.com/BeanstalkFarms/Beanstalk/pull/909).

## Links

Per the process outlined in the [BCM Emergency Response Procedures](https://docs.bean.money/almanac/governance/beanstalk/bcm-process#emergency-response-procedures), the BCM can take swift action to protect Beanstalk in the event of a bug or security vulnerability.

There is not a particular PR, commit hash, Safe transaction or Etherscan transaction for EBIP-18 given that it was executed by *not* minting the excess Unripe and Ripe Beans on Arbitrum during the L2 migration process in BIP-50.

## Problem

11,294,722.670839 Unripe Beans and their associated 3,103,598.677721 Ripe Beans were erroneously minted during the Replant of Beanstalk in August 2022 due to an incorrect calculation of user balances. These excess assets were stuck in the Beanstalk contract and were not owned by any Farmer.

## Solution

When minting Unripe and Ripe Beans on Arbitrum during the L2 migration process, avoid minting the excess amounts. This reduces the Bean supply by the ~3.1M, the number of excess Ripe Beans.

## Contract Changes

N/A — see `ReseedBean` ([`0x75C1212d7717f5aAa1179c6a71c9afc56ECddD85`](https://arbiscan.io/address/0x75C1212d7717f5aAa1179c6a71c9afc56ECddD85)), which minted Beans and Unripe Beans on Arbitrum.

## Beans Minted

None.

## Effective

Effective immediately upon the L2 migration to Arbitrum, which has already happened.
