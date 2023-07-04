# BIP-36: Silo V3

Proposed: July 3, 2023

Status: Proposed

Link: [Snapshot](https://snapshot.org/#/beanstalkdao.eth/proposal/0x177569d988e10303d1018f597b0b30f6888b20530f7b2fb699bda9838fdca70c)

---

- [Proposer](#proposer)
- [Summary](#summary)
- [Links](#links)
- [Problem](#problem)
- [Proposed Solution](#proposed-solution)
- [Technical Rationale](#technical-rationale)
- [Economic Rationale](#economic-rationale)
- [Contract Changes](#contract-changes)
- [Beans Minted](#beans-minted)
- [Audit](#audit)
- [Effective](#effective)

## Proposer

Beanstalk Farms

Proposer Wallet: [0xf9d183af486a973b7921ceb5fdc9908d12aab440](https://etherscan.io/verifySig/20935)

## Summary

- Remove the Withdrawal Freeze from the Silo entirely;
- Delay the issuance of newly Earned Beans to 10 blocks after the beginning of a Season;
- Upgrade the accounting system around Seeds per BDV to Stems such that the Seeds per BDV for a whitelisted asset can be changed after whitelisting;
- Change the Seeds per BDV rewards for both Unripe Bean (urBEAN) and Unripe BEAN:3CRV LP (urBEAN3CRV) to 0; and
- Issue Silo Deposits as ERC-1155 tokens.

## Links

- [BIP-36 GitHub PR](https://github.com/BeanstalkFarms/Beanstalk/pull/410)
- GitHub Commit Hash: [9f286e1f1b1e67bc40d35aaf4b16e5c6d83ebdd9](https://github.com/BeanstalkFarms/Beanstalk/pull/410/commits/9f286e1f1b1e67bc40d35aaf4b16e5c6d83ebdd9)
- [Safe Transaction](https://app.safe.global/transactions/tx?safe=eth:0xa9bA2C40b263843C04d344727b954A545c81D043&id=multisig_0xa9bA2C40b263843C04d344727b954A545c81D043_0x1610ee472fcd880e700392995548f6a3d00196312ba90ea1d723ae0c286e2375)

## Problem

### Withdrawal Freeze Removal

Currently there is a Withdrawal Freeze that lasts from the time of Withdrawal until the end of the Season. This provides an unnecessarily complex UX as Farmers are forced to perform 2 on-chain actions at different times (Withdraw then Claim). For example, this limits the potential for using Deposits throughout DeFi via Pipeline as Deposits cannot atomically be swapped for the underlying. Furthermore, the supply overhang creates an economic inefficiency for Beanstalk and the lockup release at the same time creates an economic disadvantage for small Farmers when exiting the Silo.

### Delayed Earned Bean Seigniorage

If the Withdrawal Freeze is removed with no changes to Earned Bean distribution, Beanstalk becomes susceptible to an attack where an account can Deposit whitelisted assets in the Silo, call `gm` and Withdraw the assets in the same transaction, gaining risk-free Bean seigniorage in the process.

### Stems Upgrade

The Seeds per BDV reward for a whitelisted asset is static and can only be set upon whitelisting. This makes it such that (1) Seeds per BDV rewards cannot easily be updated via BIP and (2) no system for autonomously updating Seeds per BDV rewards can exist.

Indexing Deposits by Season results in scenarios where Farmers may forfeit Stalk when Converting from a whitelisted asset to another with a lower Seeds per BDV.

Seeds are implemented without decimals, limiting the economic flexibility of Silo incentives.

Currently Beanstalk does not have a gas efficient way to read the total Deposited BDV in the Silo. In a future Seed Gauge System, Beanstalk may need to calculate average Seeds per BDV across all Deposited assets.

### Unripe Seed Parity at 0

Currently Beanstalk rewards 2 and 4 Seeds per BDV for urBEAN Deposits and urBEAN3CRV Deposits, respectively. There is a significant amount of Deposited urBEAN3CRV that could be Converted to repeg the Bean price. Beanstalk does not benefit from incentivizing Farmers to keep their Unripe assets in the Silo. Furthermore, the excessive accrual of Grown Stalk to Unripe asset holders during this extended period without Bean minting continues to make new Deposits less attractive. 

### ERC-1155 Deposits

Silo Deposits do not implement any standard interface, significantly limiting the composability of Bean's positive carry. Protocols such as Tractor, Depot, etc. need to implement Deposit specific functionality in order to support them. Protocols such as Seaport do not support Deposits.

## Proposed Solution

### Withdrawal Freeze Removal

Remove the Withdrawal Freeze entirely, allowing Farmers to Withdraw assets in the Silo directly to Circulating or Farm balances in a single transaction.

#### Specification

- Modify `withdrawDeposit(s)` to send the Withdrawn assets to Farm or Circulating balances using `LibTransfer.sendToken`, instead of creating a Withdrawal.
- Move `claimWithdrawal(s)` to a new `LegacyClaimWithdrawalFacet` to be used by Farmers that have unclaimed Withdrawals at the time of this upgrade.

### Delayed Earned Bean Seigniorage

Revise the distribution of Earned Beans such that they Vest after the Vesting Period after the `gm` function is called. Set the Vesting Period to 10 blocks.

In practice, this means that a Farmer who Mows their Grown Stalk during the Vesting Period will be credited with that Stalk when Earned Beans are distributed next.

#### Specification

- Split `earnedBeans` into two `uint128` variables: `earnedBeans` and `newEarnedStalk`.
- In `rewardToSilo`, set `newEarnedStalk = amount.mul(C.getStalkperBean())` (i.e., the number of new Earned Stalk is equal to the number of Beans rewarded to the Silo).
- Modify `_balanceOfEarnedBeans` to properly exclude vesting Earned Stalk when computing a Farmer's Stalk balance.
- More info: [RFC: Delayed Earned Bean Seigniorage](https://github.com/BeanstalkFarms/Beanstalk/issues/149).

### Stems Upgrade

Introduce Stems for each token on the Deposit Whitelist, where Stems represent the Stalk Grown per BDV of Deposited value. This value starts at 0 at the time of Stem deployment (the Silo V3 BIP), or when a new token is added to the Deposit Whitelist.

Index Deposits based on Stems instead of by Season. A higher Stem count corresponds to an older Deposit. Storing Deposits based on Seasons does not allow for flexibility in Seeds per BDV values. With Stems, Seeds per BDV values can now be changed via governance and can have up to 6 decimals.

Store the total Deposited BDV of each whitelisted asset in the Silo. This enables future work on a Seed Gauge System that could need to calculate the average Seeds per BDV across all Deposited assets.

Storing Deposits based on Stems enables the cleanup of the Silo V1 and V2 storage systems into a single storage method that supports ERC-1155s. This requires a migration function (`mowAndMigrate`), in which Farmers submit all of their Deposits and the function that verifies their existence, removes them from the old storage slots, and migrates them into the Stem-based slots with ERC-1155 support. 

A gas-saving migration function (`mowAndMigrateNoDeposits`) is available for accounts with no current Deposits.

As a result of the Stems migration, Farmers can now `mow` their Grown Stalk for each individual whitelisted asset. `mowMultiple` is added to allow Farmers to Mow their Grown Stalk for multiple whitelisted assets at once.

There are no penalties for waiting to migrate from Silo V2 (or V1) to Silo V3, but an account must be migrated in order to interact with the Silo again after the commitment of the Silo V3 BIP.

#### Specification

- Introduce decimals at the `stalkEarnedPerSeason` level, such that Seeds can now have up to 6 decimals.
- Modify existing `SiloFacet`, `ConvertFacet` and other facet functions from using Seeds to Stems.
- Add the `mowAndMigrate` and `mowAndMigrateNoDeposits` migration functions that migrate an account from Silo V2 (or V1) to Silo V3. 
- Split up `SiloFacet` logic into `SiloFacet`, `MigrationFacet` and `ApprovalFacet` to reduce the `SiloFacet` size.
- Add a `token` input to `mow` and introduce `mowMultiple` in `SiloFacet`.

### Unripe Seed Parity at 0

Set the Seeds per BDV for both urBEAN and urBEAN3CRV to 0.

The Beanstalk DAO expressed a preference for setting the Unripe Seeds per BDV rewards to be equal in [Temp-Check-1](https://snapshot.org/#/beanstalkfarms.eth/proposal/0xd47bc5099839f79e1d5ba0870d5c5ffff6ab416787c3ddca69d8500d9877e8e1), and specifically expressed a preference for them both being set to 0 in [Temp-Check-2](https://snapshot.org/#/beanstalkfarms.eth/proposal/0xf6a31bbdea6a0298103b217ff75418837a4fd3d5adba203cda11d1e48ae9baf6).

### ERC-1155 Deposits

Implement Silo Deposits using the ERC-1155 standard. ERC-1155s support an `id` and a `value`, matching the semi-fungible nature of Silo Deposits wherein they are non-fungible across Stems and fungible within the same number of Stems.

The ERC-20 and ERC-721 standards would not suffice for Silo Deposits:
- The ERC-20 standard would require a new deployment for each new Deposit. 
- The ERC-721 standard does not support fungibility within a given `id`.

ERC-1155 tokens will be distributed to Farmers upon `mowAndMigrate`.

#### Specification

- For each ERC-1155 token, the `id` is the concatenation of the token address (20 bytes) and the Stem of the Deposit (12 bytes). The `value` is the number of tokens.
- Add ERC-1155 events to each function that creates, removes or transfers Deposits.
- Introduce a `MetadataFacet`, which contains on-chain metadata for each ERC-1155 token.

## Technical Rationale

### Withdrawal Freeze Removal

Adding the ability to send Withdrawn assets directly to the Farmer's Farm or Circulating balance without the need for a separate transaction reduces the friction of interacting with Beanstalk.

Introducing a `LegacyClaimWithdrawalFacet` facilitates backwards compatibility for Farmers with unclaimed Withdrawals at the time of this upgrade.

### Stems Upgrade

Implementing Stems allows for Seeds per BDV to be changed via governance and set with greater precision, improving the economic flexibility of Silo incentives.

Providing a way for Beanstalk to calculate the average Seeds per BDV across all Deposited assets in a gas-efficient manner can be used to facilitate a future Seed Gauge System.

Allowing Farmers to `mow` or `mowMultiple` with distinct whitelisted assets increases the customizability of interacting with the Silo.

### ERC-1155 Deposits

Implementing Silo Deposits as ERC-1155 tokens improves the composability of Beanstalk.

## Economic Rationale

### Withdrawal Freeze Removal

Improving the composability of Beanstalk and reducing the number of separate transactions required to interact with it improves the user experience and utility of Beanstalk and Beans.

### Delayed Earned Bean Seigniorage
 
Removing the Withdrawal Freeze with no changes to Earned Bean distribution would create the opportunity to receive Bean seigniorage without taking on any exposure to the Bean price. Implementing a vesting period for Earned Beans mitigates this attack vector. In addition, a vesting period requires any related multi-block MEV attack to sustain the attack over at least that many blocks.

### Stems Upgrade

Improving the efficiency of the Silo and customizability of Seeds per BDV rewards improves the utility of Beanstalk and Beans.

Eliminating situations where Stalk is unnecessarily forfeited during Conversions reduces the friction of interacting with Beanstalk.

### Unripe Seed Parity at 0

There is an incentive not to Convert urBEAN3CRV to urBEAN given the loss of Seeds. Unripe Seed Parity reduces the incentive not to Convert urBEAN3CRV to urBEAN dramatically. Given that the potential sellable Beans to due to the Chop Penalty is so small compared to the liquidity in the pool, Beanstalk should incentivize more Converts from urBEAN3CRV to urBEAN. This should significantly contribute to peg maintenance in the short term.

There is no need to use Seeds per BDV rewards to create opportunity cost associated with Withdrawing and Redepositing Unripe assets. Excessive Grown Stalk per BDV Deposited associated with Unripe assets is likely hurting Beanstalk's ability to attract new Depositors.

### ERC-1155 Deposits

The positive carry of Beans is generally not accessible in DeFi given the lack of composability with Silo Deposits. Improving the composability of Silo Deposits by tokenizing them as ERC-1155s improves the user experience and utility of Beanstalk and Beans.

## Contract Changes

See the [Arweave upload of BIP-36](https://arweave.net/wmvXd6gI0R4E56yuzjC8K45iyp3GTG91bKEKielcDJM) to read the Contract Changes section.

## Beans Minted

None.

## Audit

The commit hash of this BIP is [9f286e1f1b1e67bc40d35aaf4b16e5c6d83ebdd9](https://github.com/BeanstalkFarms/Beanstalk/pull/410/commits/9f286e1f1b1e67bc40d35aaf4b16e5c6d83ebdd9).

Halborn has performed an audit of this BIP up to commit hash [24bf3d33355f516648b02780b4b232181afde200](https://github.com/BeanstalkFarms/Beanstalk/pull/410/commits/24bf3d33355f516648b02780b4b232181afde200). You can view the final audit report [here](https://arweave.net/rX8xO51XZSMi3n2DRMXjAl3jMWoU8gpfuxBHi9RBYag). 

Changes between the two commits:
* Changed the input `stem` type in the `RemoveDeposit(s)` event signatures in `ConvertFacet` from `int128` to `int96`;
* Changed return type for `seasonToStem` and `stemTipPerToken` from `int128` to `int96`; and
* Removed unused `Silo.sol` import removed from `ConvertFacet`.

## Effective

Immediately upon commitment.
