# BIP-38: Migrate urBEAN3CRV to urBEANETH

Proposed: October 13, 2023

Status: Proposed

Link: [Snapshot](https://snapshot.org/#/beanstalkdao.eth/proposal/0x13c1be551a3b96193ab9614814a88535152228520a1ee7bb27db84aa35413d2f)

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

Beanstalk Farms, Brendan Sanderson, Ben Weintraub

Proposer Wallet: [0x87c5e5413d60e1419fd70b17c6d299aa107efb49](https://etherscan.io/verifySig/25830)

## Summary

* Migrate the BEAN:3CRV LP tokens (BEAN3CRV) that underly the Unripe BEAN:3CRV LP token (urBEAN3CRV) to BEAN:ETH Well LP tokens (BEANETH);
* Migrate the corresponding Unripe liquidity from the BEAN3CRV pool to the BEANETH Well; and
* Implement the first step in a two step process to track Deposited BDV that has not migrated to Silo V3.

## Links

* [BIP-38 GitHub PR](https://github.com/BeanstalkFarms/Beanstalk/pull/655)
* GitHub Commit Hash: [76066733bcddb944b9af8f29acf150c02a5b8437](https://github.com/BeanstalkFarms/Beanstalk/tree/76066733bcddb944b9af8f29acf150c02a5b8437)
* [Safe Transaction](https://app.safe.global/transactions/tx?safe=eth:0xa9bA2C40b263843C04d344727b954A545c81D043&id=multisig_0xa9bA2C40b263843C04d344727b954A545c81D043_0xb66de1705121f0045f25be2f9214e3d60624d4cd3917f20c39bf8d3df583f9e7)

## Problem

As of writing, over 98% of liquidity for Beans is currently in the BEAN3CRV pool.

BEAN3CRV:
* Is exposed to the centralization risks of USDC, USDT and DAI.
* Uses an oracle that is not resistant to inter-block MEV manipulation; and
* Is subject to unnecessary trading fees that negatively affect peg maintenance.

Beanstalk currently does not support multiple Unripe LP tokens.

BDV that has not been migrated to Silo V3 is not included in `s.siloBalances[token].depositedBdv`. For Beanstalk to support a Grown Stalk Gauge System, it is necessary to track the total Deposited BDV for each whitelisted token.

## Proposed Solution

### Migration Process

Migrate the BEAN3CRV LP tokens that underly the urBEAN3CRV token to BEANETH Well LP tokens. In doing so, the liquidity underlying urBEAN3CRV is migrated to the BEANETH Well and the urBEAN3CRV token becomes the urBEANETH token (albeit, with the same token address). 

Perform the following actions by the Beanstalk Community Multisig (BCM):

| Transaction # | Protocol  | Description                                                                                  | 
|:--------------|:----------|:---------------------------------------------------------------------------------------------|
| 1             | Beanstalk | Execute BIP-38 Diamond Cut and transfer underlying BEAN3CRV to the BCM                       | 
| 2             | Curve     | Remove BEAN3CRV liquidity as Bean, USDC, USDT and DAI proportionate to the ratio of the pool | 
| 3a            | CoW Swap  | Upgrade Safe to support CoW Swap TWAP + swap USDC into ETH using TWAP order                  | 
| 3b            | CoW Swap  | Swap USDT into ETH using TWAP order                                                          | 
| 3c            | CoW Swap  | Swap DAI into ETH using TWAP order                                                           | 
| 4             | Basin     | Add Beans from Step (2) and ETH from Step (3) as liquidity to the BEANETH Well               | 
| 5             | Beanstalk | Add BEANETH LP as underlying token for the new urBEANETH                                     | 

In order to minimize the risk of the swap being frontrun (for example, immediately after the passage of the BIP), authorize the BCM to begin the migration process at any point within 30 days of the passage of this BIP.

During the migration process, attempts to Deposit, Convert or Chop Unripe assets will revert. After the migration process is complete, Conversions between urBEAN and urBEANETH will Convert in/out of the BEANETH Well instead of the BEAN3CRV pool.

The cost of slippage during Step (3) is passed onto the Unripe LP holders.

`addMigratedUnderlying` is added to the Unripe Facet to migrate the underlying token of an Unripe token to a new token.

Due to the multi-step nature of this BIP, BCM Signers are only expected to submit Etherscan verified messages for the initial Diamond Cut transaction.

### Fertilizer Changes

Upgrade the Barn to add liquidity to the BEANETH Well instead of the BEAN3CRV pool upon each Fertilizer purchase.

As a result:
* Fertilizer is bought with WETH instead of USDC;
* The amount of Fertilizer received after purchase and increase in amount recapitalized is equal to the value of the WETH in USDC at the time of purchase;
* The `mintFertilizer` function contains a `minFertilizerOut` parameter to protect against substantial movement in the ETH price; and
* The `addFertilizerOwner` function is removed.

### Silo V3 Unmigrated BDV

A two step process is required to start properly tracking the total Deposited BDV of each whitelisted token. Implement the first step by adding a counter (`s.migratedBdvs`) that tracks the amount of BDV that has been migrated to Silo V3 for each token.

In the second step, the total Deposited BDV for each token is incremented by the remaining unmigrated BDV at the BIP-38 commitment block subtracted by the BDV that has been migrated since that block as stored on-chain. This second step must be implemented in a future BIP.

## Technical Rationale

Given that the migration process cannot be executed atomically and Unripe asset functionality will be in a broken state during it, attempts to Deposit, Convert or Chop Unripe assets during the migration process will revert. 

The Unripe Facet must be updated to add support for migrating the Unripe LP token to a new underlying asset.

The `mintFertilizer` function must be updated to include the `minFertilizerOut` parameter to account for potential movement in the ETH price between transaction submission and execution. The `addFertilizerOwner` function is removed given that it would need to be updated to reflect the migration to urBEANETH, however it was only expected to be called once during the Replant.

The Convert Facet and Convert Getters Facet must be updated due to function changes in `LibUnripeConvert.sol` and `LibWellConvert.sol`.

The Metadata Facet must be updated in order to update the ERC-1155 metadata for urBEANETH Deposits.

The BDV Facet must be updated due to `unripeLPToBDV` being changed to reflect the migration to BEANETH.

The Migration Facet must be updated due to the introduction of the migrated BDV counter.

## Economic Rationale

Beans primarily trade against 3CRV which is a significant centralization vector for Beanstalk. Ether is the most decentralized, censorship resistant and liquid asset on the Ethereum network.

For as long as most liquidity for Beans is in the BEAN3CRV pool, Beanstalk is still subject to inter-block MEV manipulation. The Multi Flow Pump on the BEANETH Well is the first Ethereum-native oracle for Ethereum-native data that offers inter-block MEV manipulation resistance in a post-Merge environment.

For as long as most volume for Beans is in the BEAN3CRV pool, most trades with Beans are still subject to an unnecessary trading fee in the pool, which results in worse prices for Farmers and worse peg maintenance for Beanstalk. The Constant Product 2 Well Function used by the BEANETH Well has no trading fee.

The price of 3CRV is dependent on the worst of the prices of USDC, USDT and DAI. ETH is not subject to the same risk.

## Contract Changes

### Initialization Contract

The `init` function on the following `InitMigrateUnripeBean3CrvToBeanEth` contract is called:

- [`0x810468cbC28ecb522C10cB53FEC9e387F1ebc84D`](https://etherscan.io/address/0x810468cbC28ecb522C10cB53FEC9e387F1ebc84D#code)

### Unripe Facet

The following `UnripeFacet` is removed from Beanstalk:

- [`0x261b3ae660504537fbfe15b6c1c664976344eb0a`](https://etherscan.io/address/0x261b3ae660504537fbfe15b6c1c664976344eb0a#code)

The following `UnripeFacet` is added to Beanstalk:

- [`0xAa8c44D0B89864b467C3776a7Dd367ff2aB6992A`](https://etherscan.io/address/0xAa8c44D0B89864b467C3776a7Dd367ff2aB6992A#code)

#### `UnripeFacet` Function Changes

| Name                           | Selector     | Action  | Type | New Functionality |
|:-------------------------------|:-------------|:--------|:-----|:------------------|
| `_getPenalizedUnderlying`      | `0xa84643e4` | Replace | View |                   |
| `balanceOfPenalizedUnderlying` | `0x1acc0a47` | Replace | View |                   |
| `balanceOfUnderlying`          | `0x1acc0a47` | Replace | View |                   |
| `getPenalizedUnderlying`       | `0x6de45df2` | Replace | View |                   |
| `getPenalty`                   | `0x014a8a49` | Replace | View |                   |
| `getPercentPenalty`            | `0xbb7de478` | Replace | View |                   |
| `getRecapFundedPercent`        | `0x43cc4ee0` | Replace | View |                   |
| `getRecapPaidPercent`          | `0xab434eb7` | Replace | View |                   |
| `getTotalUnderlying`           | `0xadef4533` | Replace | View |                   |
| `getUnderlying`                | `0x9f06b3fa` | Replace | View |                   |
| `getUnderlyingPerUnripeToken`  | `0xb8a04d1b` | Replace | View |                   |
| `getUnderlyingToken`           | `0x691bcc88` | Replace | View |                   |
| `isUnripe`                     | `0xfc6a19df` | Replace | View |                   |
| `picked`                       | `0xd3c73ec8` | Replace | View |                   |
| `addMigratedUnderlying`        | `0x787cee99` | Add     | Call | ✓                 |
| `addUnripeToken`               | `0xfa345569` | Replace | Call |                   |
| `chop`                         | `0x9a516cad` | Replace | Call | ✓                 |
| `pick`                         | `0x13ed3cea` | Replace | Call |                   |
| `switchUnderlyingToken`        | `0xa33fa99f` | Add     | Call | ✓                 |

### Fertilizer Facet

The following `FertilizerFacet` is removed from Beanstalk:

- [`0xFC7Ed192a24FaB3093c8747c3DDBe6Cacd335B6C`](https://etherscan.io/address/0xFC7Ed192a24FaB3093c8747c3DDBe6Cacd335B6C#code)

The following `FertilizerFacet` is added to Beanstalk:

- [`0x3FA7ECcfbFDF4407932D2318401d20464189C5F1`](https://etherscan.io/address/0x3FA7ECcfbFDF4407932D2318401d20464189C5F1#code)

#### `FertilizerFacet` Function Changes

| Name                        | Selector     | Action  | Type | New Functionality |
|:----------------------------|:-------------|:--------|:-----|:------------------|
| `balanceOfBatchFertilizer`  | `0x304ec65d` | Replace | View |                   |
| `balanceOfFertilized`       | `0xb6f42085` | Replace | View |                   |
| `balanceOfFertilizer`       | `0x1799b3b2` | Replace | View |                   |
| `balanceOfUnfertilized`     | `0x1edb6be1` | Replace | View |                   |
| `beansPerFertilizer`        | `0x9bb4e35a` | Replace | View |                   |
| `getActiveFertilizer`       | `0xdc6ba285` | Replace | View |                   |
| `getCurrentHumidity`        | `0x39448802` | Replace | View |                   |
| `getEndBpf`                 | `0xc85951a1` | Replace | View |                   |
| `getFertilizer`             | `0x9c45a1d5` | Replace | View |                   |
| `getFertilizers`            | `0x34af5416` | Replace | View |                   |
| `getFirst`                  | `0x1e223143` | Replace | View |                   |
| `getHumidity`               | `0x29130a66` | Replace | View |                   |
| `getLast`                   | `0x4d622831` | Replace | View |                   |
| `getMintFertilizerOut`      | `0x69744dd0` | Add     | View | ✓                 |
| `getNext`                   | `0xf4a057e2` | Replace | View |                   |
| `isFertilizing`             | `0x6ae1c014` | Replace | View |                   |
| `remainingRecapitalization` | `0x4a16607c` | Replace | View |                   |
| `totalFertilizedBeans`      | `0x4f9a9678` | Replace | View |                   |
| `totalFertilizerBeans`      | `0xf9c4ebde` | Replace | View |                   |
| `totalUnfertilizedBeans`    | `0xa3ef48c9` | Replace | View |                   |
| `addFertilizerOwner`        | `0x8cd31ca0` | Remove  | Call |                   |
| `claimFertilized`           | `0x83e08888` | Replace | Call |                   |
| `mintFertilizer`            | `0x0bfca7e3` | Remove  | Call |                   |
| `mintFertilizer`            | `0xbb02e10b` | Add     | Call | ✓                 |
| `payFertilizer`             | `0xd47aee59` | Replace | Call |                   |

### Convert Facet

The following `ConvertFacet` is removed from Beanstalk:

- [`0xC2f8F1412d10E4DC79D34a46ab1d3d862517f939`](https://etherscan.io/address/0xC2f8F1412d10E4DC79D34a46ab1d3d862517f939#code)

The following `ConvertFacet` is added to Beanstalk:

- [`0xDc6B4ef6bA55706B19Bd389eA446d232eFb4E5D4`](https://etherscan.io/address/0xDc6B4ef6bA55706B19Bd389eA446d232eFb4E5D4#code)

#### `ConvertFacet` Function Changes

| Name                  | Selector     | Action   | Type | New Functionality |
|:----------------------|:-------------|:---------|:-----|:------------------|
| `convert`             | `0xb362a6e8` | Replace  | Call |                   |

### Convert Getters Facet

The following `ConvertGettersFacet` is removed from Beanstalk:

- [`0x912f505ecD6536733da22BB4349595aA36806918`](https://etherscan.io/address/0x912f505ecD6536733da22BB4349595aA36806918#code)

The following `ConvertGettersFacet` is added to Beanstalk:

- [`0x789e37096Fb0abbD4f64A86B51D720b371853a70`](https://etherscan.io/address/0x789e37096Fb0abbD4f64A86B51D720b371853a70#code)

#### `ConvertGettersFacet` Function Changes

| Name                  | Selector     | Action   | Type | New Functionality |
|:----------------------|:-------------|:---------|:-----|:------------------|
| `getAmountOut`        | `0x4aa06652` | Replace  | View |                   |
| `getMaxAmountIn`      | `0x24dd285c` | Replace  | View |                   |

### Metadata Facet

The following `MetadataFacet` is removed from Beanstalk:

- [`0x5e6991aFa1352822e769c873200954B4dE6c6E48`](https://etherscan.io/address/0x5e6991aFa1352822e769c873200954B4dE6c6E48#code)

The following `MetadataFacet` is added to Beanstalk:

- [`0x8aD8dfC3303469A8c2d14763199a99363bF580cc`](https://etherscan.io/address/0x8aD8dfC3303469A8c2d14763199a99363bF580cc#code)

#### `MetadataFacet` Function Changes

| Name                   | Selector     | Action  | Type | New Functionality |
|:-----------------------|:-------------|:--------|:-----|:------------------|
| `imageURI`             | `0xc20b8071` | Replace | View |                   |
| `name`                 | `0x06fdde03` | Replace | View |                   |
| `symbol`               | `0x95d89b41` | Replace | View |                   |
| `uri`                  | `0x0e89341c` | Replace | View |                   |

### BDV Facet

The following `BDVFacet` is removed from Beanstalk:

- [`0x9Cb54A8eAcD4d295dd02833cd2bdD385173c7fF5`](https://etherscan.io/address/0x9Cb54A8eAcD4d295dd02833cd2bdD385173c7fF5#code)

The following `BDVFacet` is added to Beanstalk:

- [`0xC1Bbee46EcB6445B176F7f172F91976ADF4e21D9`](https://etherscan.io/address/0xC1Bbee46EcB6445B176F7f172F91976ADF4e21D9#code)

#### `BDVFacet` Function Changes

| Name                   | Selector     | Action  | Type | New Functionality |
|:-----------------------|:-------------|:--------|:-----|:------------------|
| `beanToBDV`            | `0x5a049a47` | Replace | View |                   |
| `curveToBDV`           | `0xf984019b` | Replace | View |                   |
| `unripeBeanToBDV`      | `0xc8cda2a0` | Replace | View |                   |
| `unripeLPToBDV`        | `0xb0c22bb1` | Replace | View | ✓                 |
| `wellBdv`              | `0xc84c7727` | Replace | View |                   |

### Migration Facet

The following `MigrationFacet` is removed from Beanstalk:

- [`0x141209527f95540e0b018e56edf5a59e1339437f`](https://etherscan.io/address/0x141209527f95540e0b018e56edf5a59e1339437f#code)

The following `MigrationFacet` is added to Beanstalk:

- [`0x9F2444e6cFAAB6ea16Fc05B989f1017508F84A41`](https://etherscan.io/address/0x9F2444e6cFAAB6ea16Fc05B989f1017508F84A41#code)

#### `MigrationFacet` Function Changes

| Name                                     | Selector     | Action  | Type | New Functionality |
|:-----------------------------------------|:-------------|:--------|:-----|:------------------|
| `balanceOfGrownStalkUpToStemsDeployment` | `0x505f43ea` | Replace | View |                   |
| `balanceOfLegacySeeds`                   | `0x1be2cfd8` | Replace | View |                   |
| `getDepositLegacy`                       | `0xa9be1acb` | Replace | View |                   |
| `mowAndMigrate`                          | `0x1f4f3d55` | Replace | Call |                   |
| `mowAndMigrateNoDeposits`                | `0xaed942e9` | Replace | Call |                   |
| `totalMigratedBdv`                       | `0x2b8cde0d` | Add     | View | ✓                 |

### Event Changes

| Name                      | Change                                            |
|:--------------------------|:--------------------------------------------------|
| `SwitchUnderlyingToken`   | New event                                         |

## Beans Minted 

None.

## Audit

The commit hash of this BIP is [76066733bcddb944b9af8f29acf150c02a5b8437](https://github.com/BeanstalkFarms/Beanstalk/tree/76066733bcddb944b9af8f29acf150c02a5b8437).

Cyfrin has performed an audit of commit hash [12c608a22535e3a1fe379db1153185fe43851ea7](https://github.com/BeanstalkFarms/Beanstalk/tree/12c608a22535e3a1fe379db1153185fe43851ea7) and then reviewed remediations included in commit hash [76066733bcddb944b9af8f29acf150c02a5b8437](https://github.com/BeanstalkFarms/Beanstalk/tree/76066733bcddb944b9af8f29acf150c02a5b8437). 

The final audit report can be found [here](https://arweave.net/PxEMjx4knNBHZigPvQ9DFjJzvtqv_8yfEBbCo_wL5fU). 

## Effective

No upgrades are necessarily executed by the BCM immediately upon the passage of this BIP.

The BCM has up to 30 days after the passage of this BIP to start the migration outlined in *Migration Process*.
