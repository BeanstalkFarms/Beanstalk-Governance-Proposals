# BIP-48: Whitelist BEANwstETH and Migrate Unripe Liquidity

Proposed: July 19, 2024

Status: Proposed

Link: [Snapshot](https://snapshot.org/#/beanstalkdao.eth/proposal/0x7a9167d3e201225a3352b4643c9aeaac8916b8cb018796bd72b95adf6f0c5bbd)

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

Proposer Wallet: [0xf1a621fe077e4e9ac2c0cefd9b69551db9c3f657](https://etherscan.io/verifySig/253716)

## Summary

* Add the BEAN:wstETH Constant Product 2 Well with Multi Flow Pump v1.1.0 (BEANwstETH) to the Deposit and Minting Whitelists;
* Add the BEANwstETH → BEAN and BEAN → BEANwstETH Conversions to the Convert Whitelist;
* Migrate the Unripe liquidity (*i.e.*, BEAN:ETH Well LP tokens) from the BEANETH Well to the BEANwstETH Well;
* Accordingly, migrate urBEANETH tokens to urBEANwstETH tokens; and
* Update the ETH:USD oracle to no longer use Uniswap V3 and instead rely exclusively on Chainlink.

## Links

* [BIP-48 GitHub PR](https://github.com/BeanstalkFarms/Beanstalk/pull/758)
* GitHub Commit Hash: [45afeaf1f9c57bcdf506336aff63fa8805a1081f](https://github.com/BeanstalkFarms/Beanstalk/pull/758/commits/45afeaf1f9c57bcdf506336aff63fa8805a1081f)
* [Safe Transaction](https://app.safe.global/transactions/tx?safe=eth:0xa9bA2C40b263843C04d344727b954A545c81D043&id=multisig_0xa9bA2C40b263843C04d344727b954A545c81D043_0x5765a2aaf25f2a2d41abdc8602e41b5746684e9e82df02cca85d97a6286fc5be)

## Problem

Opportunities for Bean liquidity providers to receive Stalk and Seed rewards are currently limited. A wider variety of options increases utility for Silo Depositors and will be possible with the combination of the [Seed Gauge System](https://github.com/BeanstalkFarms/Beanstalk/issues/726) and [Generalized Convert](https://github.com/BeanstalkFarms/Beanstalk/issues/716).

Beanstalk could be offering higher yield opportunities to Bean liquidity providers, including Unripe LP holders, via the ETH staking yield with minimal additional trust assumptions.

As of writing, over 99% of liquidity for Beans is currently in the BEANETH Well, which uses Multi Flow v1.0.0. Multi Flow v1.0.0 does not have a way to change the exchange rate between Well reserves when the cap on the maximum percent change in reserves per block is reached (see [EBIP-9](https://arweave.net/uhIRmKbQM8N_ohjqs9wA_3RBn8AjhGbH_wOPaXu3be4)).

Beanstalk currently does not support multiple Unripe LP tokens.

Currently, the ETH:USD oracle defaults to using Chainlink when the Chainlink price is greater than 1% different than the prices in the ETH:USDC and ETH:USDT Uniswap V3 pools (which is considered manipulation).

## Proposed Solution

Whitelist a BEAN:wstETH Constant Product 2 Well with Multi Flow Pump v1.1.0 for Deposit in the Silo.

We propose to set the initial Gauge Points of BEANwstETH to 400 and set the optimal distribution of the LP Seed Gauge System to 80% BEANwstETH and 20% BEANETH.

### wstETH:USD Oracle Design

Beanstalk requires a wstETH:USD oracle in order to compute deltaB in the BEAN:wstETH Well.

The oracle reads from multiple data sources and calculates the wstETH:ETH price using 3 separate methods:
1. [The wstETH:stETH redemption rate (A)](https://etherscan.io/address/0x7f39C581F595B53c5cb19bD0b3f8dA6c935E2Ca0) * the [stETH:ETH Chainlink price feed (B)](https://etherscan.io/address/0x86392dC19c0b719886221c78AB11eb8Cf5c52812);
2. [The wstETH:WETH Uniswap V3 pool](https://etherscan.io/address/0x109830a1AAaD605BbF02a9dFA7B0B92EC2FB7dAa); and
3. (A).

And computes a final wstETH:ETH price via the following logic:

* If (1) and (2) are within 1%;
    * If the average of (1) and (2) is greater than (A):
        * Return (A);
    * Else, return the average of (1) and (2));
* Else, return 0 (fail).

Multiplying the wstETH:ETH and ETH:USD prices results in the final wstETH:USD price.

### Multi Flow v1.1.0

Multi Flow v1.1.0 introduces the ability to set the maximum percent change in exchange rate between any two tokens in a Well per block. We propose setting this parameter to 0.5%.

Multi Flow v1.1.0 also introduces the ability the set the maximum percent change in LP token supply in a Well per block. We propose setting this parameter to 3%.

Because the existing BEANETH Well uses Multi Flow v1.0.0 which does not contain these improvements, we propose turning off minting from the BEANETH Well for 24 Seasons upon the deployment of this BIP while the reserves stored in its Pump catch up to the reserves in the Well post-Unripe liquidity migration. 

### Migration Process

Migrate the BEANETH Well LP tokens that underly the urBEANETH token to BEANwstETH Well LP tokens. In doing so, the liquidity underlying urBEANETH is migrated to the BEANwstETH Well and the urBEANETH token becomes the urBEANwstETH token (albeit, with the same token address). 

| Transaction # | Protocol  | Description                                                                                  | 
|:--------------|:----------|:---------------------------------------------------------------------------------------------|
| 1             | Beanstalk | Execute the BIP Diamond Cut and transfer underlying BEANETH to the BCM                       | 
| 2             | Basin     | Remove BEANETH liquidity as Bean and WETH proportionate to the ratio of the pool             | 
| 3             | CoW Swap  | Swap WETH into stETH using TWAP order*                                                       | 
| 4             | Lido      | Wrap stETH into wstETH                                                                       | 
| 5             | Basin     | Add Beans from Step (2) and wstETH from Step (4) as liquidity to the BEANwstETH Well         | 
| 6             | Beanstalk | Add BEANwstETH LP as underlying token for the new urBEANwstETH                               | 

**If the DAO can get a better execution price by directly staking WETH for stETH (rather than swapping), the BCM will do this.*

During the migration process, attempts to Deposit, Convert or Chop Unripe assets will revert. After the migration process is complete, Conversions between urBEAN and urBEANwstETH will Convert in/out of the BEANwstETH Well instead of the BEANETH Well.

The cost of slippage during Step (3) is passed on to Unripe LP holders.

#### Fertilizer Changes

Upgrade the Barn to add liquidity to the BEANwstETH Well instead of the BEANETH Well upon each Fertilizer purchase.

As a result:
* Fertilizer is bought with wstETH instead of WETH; and
* The amount of Fertilizer received after purchase and increase in amount recapitalized is equal to the value of the wstETH in USD at the time of purchase.

### ETH:USD Oracle Design

Beanstalk requires an ETH:USD oracle in order to compute deltaB in the BEAN:ETH and BEAN:wstETH Wells. We propose to remove Uniswap V3 as an input to this price oracle, such that Beanstalk calculates the ETH:USD price exclusively using Chainlink.

## Technical Rationale

The Season Facet must be updated to generalize `stepOracle` to support minting in any whitelisted Well.

Given that the migration process cannot be executed atomically and Unripe asset functionality will be in a broken state during it, attempts to Deposit, Convert or Chop Unripe assets during the migration process will revert.

The Fertilizer Facet must be updated to use the new Barn Raise token (wstETH), which is now abstracted away via the new `LibBarnRaise` library.

The Unripe Facet, BDV Facet, `LibConvert`, `LibUnripeConvert`, `LibEvaluate` and `LibFertilizer` must be updated to reflect the use of `LibBarnRaise`.

All of the oracle libraries are updated to be generalized to support reading data for any tokens that Beanstalk supports.

## Economic Rationale

Beanstalk could be offering higher yield opportunities to Bean liquidity providers (and Unripe LP holders) via the ETH staking yield with minimal additional trust assumptions. stETH has the largest TVL of any liquid staking derivative. All other liquid staking derivatives with reasonable market adoption – a requirement for sufficient decentralization – still have arbitrary and unilateral upgradability privileges held by a small party.

Basin does not have a Well Implementation that tracks rebasing tokens. Thus, Basin (and thus, Beanstalk) must use wstETH (*i.e.*, the wrapped, non-rebasing version of stETH).

Setting the optimal LP BDV distribution to 80% BEANwstETH and 20% BEANETH reflects the preference for higher yield opportunities to Depositors. Setting the BEANwstETH Gauge Points to 400 initializes the Gauge Point distribution with the same proportions as the optimal LP BDV distribution (BEANETH currently has 100 Gauge Points).

There is no wstETH:USD Chainlink feed on Ethereum mainnet. Using 3 sources for the wstETH:ETH prices increases accuracy and reliability.

For as long as most liquidity for Beans is in the existing BEANETH Well, Beanstalk is still subject to risks associated with Multi Flow v1.0.0 that were pointed out in EBIP-9.

Setting the percent exchange rate change per block cap and percent LP token supply change per block cap in Multi Flow v1.1.0 to 0.5% and 3%, respectively, minimizes both potential inter-block MEV manipulation and instances where the Pump reserves are out of sync with the Well reserves. Investigation of past data in the ETH:USDC and ETH:USDT Uniswap V2 pools can be found [here](https://github.com/BeanstalkFarms/MultiFlowAnalysis).

The Uniswap V3 component of the existing ETH:USD oracle serves little to no purpose given that it already defaults to Chainlink in the case of manipulation.

## Contract Changes

### Initialization Contract

The `init` function on the following `InitMigrateUnripeBeanEthToBeanSteth` contract is called:

- [`0x15A2053B3d559d19FeD2D7FC429304e837cEFa00`](https://etherscan.io/address/0x15A2053B3d559d19FeD2D7FC429304e837cEFa00#code)

### BDV Facet

The following `BDVFacet` is being removed from Beanstalk:

- [`0xB752bfD626AD8715DE26D9bf3b3512F13632CCa1`](https://etherscan.io/address/0xB752bfD626AD8715DE26D9bf3b3512F13632CCa1#code)

The following `BDVFacet` is added to Beanstalk:

- [`0xaef84C2B863aB845F59f672d958B175262dcfc89`](https://etherscan.io/address/0xaef84C2B863aB845F59f672d958B175262dcfc89#code)

#### `BDVFacet` Function Changes

| Name                   | Selector     | Action  | Type | New Functionality |
|:-----------------------|:-------------|:--------|:-----|:------------------|
| `beanToBDV`            | `0x5a049a47` | Replace | Read |                   |
| `curveToBDV`           | `0xf984019b` | Replace | Read |                   |
| `unripeBeanToBDV`      | `0xc8cda2a0` | Replace | Read |                   |
| `unripeLPToBDV`        | `0xb0c22bb1` | Replace | Read | ✓                 |
| `wellBdv`              | `0xc84c7727` | Replace | Read |                   |

### Convert Facet

The following `ConvertFacet` is removed from Beanstalk:

- [`0x1C55d002bf78Ced8cb4ebd8F4Cf39Ff93835C934`](https://etherscan.io/address/0x1C55d002bf78Ced8cb4ebd8F4Cf39Ff93835C934#code)

The following `ConvertFacet` is added to Beanstalk:

- [`0xEb1b833D3E81cb3d390514cabB9b809E6170626C`](https://etherscan.io/address/0xEb1b833D3E81cb3d390514cabB9b809E6170626C#code)

#### `ConvertFacet` Function Changes

| Name                  | Selector     | Action   | Type  | New Functionality |
|:----------------------|:-------------|:---------|:------|:------------------|
| `convert`             | `0xb362a6e8` | Replace  | Write | ✓                 |

### Convert Getters Facet

The following `ConvertGettersFacet` is removed from Beanstalk:

- [`0x0a4121F3c4ACd9825Ed5499ACAD9fEA7a8a4eeED`](https://etherscan.io/address/0x0a4121F3c4ACd9825Ed5499ACAD9fEA7a8a4eeED#code)

The following `ConvertGettersFacet` is added to Beanstalk:

- [`0x8ABa09526dc6EB6Ea44eE3f8745dD8bc9EF744E2`](https://etherscan.io/address/0x8ABa09526dc6EB6Ea44eE3f8745dD8bc9EF744E2#code)

#### `ConvertGettersFacet` Function Changes

| Name                  | Selector     | Action   | Type | New Functionality |
|:----------------------|:-------------|:---------|:-----|:------------------|
| `getAmountOut`        | `0x4aa06652` | Replace  | View |                   |
| `getMaxAmountIn`      | `0x24dd285c` | Replace  | View |                   |

### Enroot Facet

The following `EnrootFacet` is removed from Beanstalk:

- [`0x3780b8268F19118E7e44B9FEF6CA090bC5E077e6`](https://etherscan.io/address/0x3780b8268F19118E7e44B9FEF6CA090bC5E077e6#code)

The following `EnrootFacet` is added to Beanstalk:

- [`0x179Bb2636F0066d837f1a446083A0FBA131c1A46`](https://etherscan.io/address/0x179Bb2636F0066d837f1a446083A0FBA131c1A46#code)

#### `EnrootFacet` Function Changes

| Name                   | Selector     | Action  | Type  | New Functionality |
|:-----------------------|:-------------|:--------|:------|:------------------|
| `enrootDeposit`        | `0x0b58f073` | Replace | Write |                   |
| `enrootDeposits`       | `0x88fcd169` | Replace | Write |                   |

### Fertilizer Facet

The following `FertilizerFacet` is removed from Beanstalk:

- [`0x3FA7ECcfbFDF4407932D2318401d20464189C5F1`](https://etherscan.io/address/0x3FA7ECcfbFDF4407932D2318401d20464189C5F1#code)

The following `FertilizerFacet` is added to Beanstalk:

- [`0x729672c68134E2DF0CdD36D3296841A2993534c7`](https://etherscan.io/address/0x729672c68134E2DF0CdD36D3296841A2993534c7#code)

#### `FertilizerFacet` Function Changes

| Name                        | Selector     | Action  | Type  | New Functionality |
|:----------------------------|:-------------|:--------|:------|:------------------|
| `mintFertilizer`            | `0xbb02e10b` | Remove  | Write |                   |
| `_getMintFertilizerOut`     | `0x94daa221` | Add     | Read  | ✓                 |
| `beginBarnRaiseMigration`   | `0xe3d4e44c` | Add     | Write | ✓                 |
| `getBarnRaiseToken`         | `0xf255da60` | Add     | Read  | ✓                 |
| `getBarnRaiseWell`          | `0x93a39bea` | Add     | Read  | ✓                 |
| `mintFertilizer`            | `0x363591d0` | Add     | Write | ✓                 |
| `balanceOfBatchFertilizer`  | `0x304ec65d` | Replace | Read  |                   |
| `balanceOfFertilized`       | `0xb6f42085` | Replace | Read  |                   |
| `balanceOfFertilizer`       | `0x1799b3b2` | Replace | Read  |                   |
| `balanceOfUnfertilized`     | `0x1edb6be1` | Replace | Read  |                   |
| `beansPerFertilizer`        | `0x9bb4e35a` | Replace | Read  |                   |
| `claimFertilized`           | `0x83e08888` | Replace | Write |                   |
| `getActiveFertilizer`       | `0xdc6ba285` | Replace | Read  |                   |
| `getCurrentHumidity`        | `0x39448802` | Replace | Read  |                   |
| `getEndBpf`                 | `0xc85951a1` | Replace | Read  |                   |
| `getFertilizer`             | `0x9c45a1d5` | Replace | Read  |                   |
| `getFertilizers`            | `0x34af5416` | Replace | Read  |                   |
| `getFirst`                  | `0x1e223143` | Replace | Read  |                   |
| `getHumidity`               | `0x29130a66` | Replace | Read  |                   |
| `getLast`                   | `0x4d622831` | Replace | Read  |                   |
| `getMintFertilizerOut`      | `0x69744dd0` | Replace | Read  | ✓                 |
| `getNext`                   | `0xf4a057e2` | Replace | Read  |                   |
| `isFertilizing`             | `0x6ae1c014` | Replace | Read  |                   |
| `payFertilizer`             | `0xd47aee59` | Replace | Write |                   |
| `remainingRecapitalization` | `0x4a16607c` | Replace | Read  |                   |
| `totalFertilizedBeans`      | `0x4f9a9678` | Replace | Read  |                   |
| `totalFertilizerBeans`      | `0xf9c4ebde` | Replace | Read  |                   |
| `totalUnfertilizedBeans`    | `0xa3ef48c9` | Replace | Read  |                   |

### Metadata Facet

The following `MetadataFacet` is being removed from Beanstalk:

- [`0x9F5ec59d13AfDb581A383D6215b717312e875Fd2`](https://etherscan.io/address/0x9F5ec59d13AfDb581A383D6215b717312e875Fd2#code)

The following `MetadataFacet` is added to Beanstalk:

- [`0x0d8f6F09a2B806d406d511C113f2Fc3F4D608Fc1`](https://etherscan.io/address/0x0d8f6F09a2B806d406d511C113f2Fc3F4D608Fc1#code)

#### `MetadataFacet` Function Changes

| Name                   | Selector     | Action  | Type  | New Functionality |
|:-----------------------|:-------------|:--------|:------|:------------------|
| `imageURI`             | `0xc20b8071` | Replace | Read  |                   |
| `name`                 | `0x06fdde03` | Replace | Read  |                   |
| `symbol`               | `0x95d89b41` | Replace | Read  |                   |
| `uri`                  | `0x0e89341c` | Replace | Read  |                   |

### Season Facet

The following `SeasonFacet` is removed from Beanstalk:

- [`0xB5818dE8b02394b4300F15F61083dc3ff976EAA1`](https://etherscan.io/address/0xB5818dE8b02394b4300F15F61083dc3ff976EAA1#code)

The following `SeasonFacet` is added to Beanstalk:

- [`0x92458b7ade7798c45E5ff583c353F70F950d66Cf`](https://etherscan.io/address/0x92458b7ade7798c45E5ff583c353F70F950d66Cf#code)

#### `SeasonFacet` Function Changes

| Name                 | Selector     | Action  | Type  | New Functionality |
|:---------------------|:-------------|:--------|:------|:------------------|
| `gm`                 | `0x64ee4b80` | Replace | Write | ✓                 |
| `seasonTime`         | `0xca7b7d7b` | Replace | Read  |                   |
| `sunrise`            | `0xfc06d2a6` | Replace | Write |                   |

### Season Getters Facet

The following `SeasonGettersFacet` is removed from Beanstalk:

- [`0x46d11a5076EAAD1ffA24b0C2DDF38d4Aeaa19920`](https://etherscan.io/address/0x46d11a5076EAAD1ffA24b0C2DDF38d4Aeaa19920#code)

The following `SeasonGettersFacet` is added to Beanstalk:

- [`0x5A1675f3156c9e73D7eA20eb58470A0002865E85`](https://etherscan.io/address/0x5A1675f3156c9e73D7eA20eb58470A0002865E85#code)

#### `SeasonGettersFacet` Function Changes

| Name                                   | Selector     | Action  | Type  | New Functionality |
|:---------------------------------------|:-------------|:--------|:------|:------------------|
| `abovePeg`                             | `0x2a27c499` | Replace | Read  |                   |
| `calcGaugePointsWithParams`            | `0xeedc7ab9` | Replace | Read  |                   |
| `getAverageGrownStalkPerBdv`           | `0x7ba6cbf8` | Replace | Read  |                   |
| `getAverageGrownStalkPerBdvPerSeason`  | `0xeb0e1215` | Replace | Read  |                   |
| `getBeanEthGaugePointsPerBdv`          | `0xd1db56b8` | Replace | Read  |                   |
| `getBeanGaugePointsPerBdv`             | `0x69aa7e02` | Replace | Read  |                   |
| `getBeanToMaxLpGpPerBdvRatio`          | `0xcc88d4f9` | Replace | Read  |                   |
| `getBeanToMaxLpGpPerBdvRatioScaled`    | `0x673c75f0` | Replace | Read  |                   |
| `getDeltaPodDemand`                    | `0x64b3496b` | Replace | Read  |                   |
| `getGaugePoints`                       | `0x93523425` | Replace | Read  |                   |
| `getGaugePointsPerBdvForToken`         | `0x64887852` | Replace | Read  |                   |
| `getGaugePointsPerBdvPerWell`          | `0xb2b0556d` | Replace | Read  |                   |
| `getGaugePointsWithParams`             | `0x141933bf` | Replace | Read  |                   |
| `getGrownStalkIssuedPerGp`             | `0xf98da2de` | Replace | Read  |                   |
| `getGrownStalkIssuedPerSeason`         | `0x383f170f` | Replace | Read  |                   |
| `getLargestLiqWell`                    | `0xd1943f7f` | Replace | Read  |                   |
| `getLiquidityToSupplyRatio`            | `0xcb2d0a3c` | Replace | Read  |                   |
| `getPodRate`                           | `0xcce813a1` | Replace | Read  |                   |
| `getSeedGauge`                         | `0x6af8e5a4` | Replace | Read  |                   |
| `getSopWell`                           | `0x7d23804d` | Replace | Read  |                   |
| `getTotalBdv`                          | `0x50539159` | Replace | Read  |                   |
| `getTotalUsdLiquidity`                 | `0xbbf459a7` | Replace | Read  |                   |
| `getTotalWeightedUsdLiquidity`         | `0xf788b47c` | Replace | Read  |                   |
| `getTwaLiquidityForWell`               | `0xa13a3742` | Replace | Read  |                   |
| `getWeightedTwaLiquidityForWell`       | `0x93c9e531` | Replace | Read  |                   |
| `paused`                               | `0x5c975abb` | Replace | Read  |                   |
| `plentyPerRoot`                        | `0xe60d7a83` | Replace | Read  |                   |
| `poolDeltaB`                           | `0x471bcdbe` | Replace | Read  |                   |
| `rain`                                 | `0x43def26e` | Replace | Read  |                   |
| `season`                               | `0xc50b0fb0` | Replace | Read  |                   |
| `sunriseBlock`                         | `0x3b2ecb70` | Replace | Read  |                   |
| `time`                                 | `0x16ada547` | Replace | Read  |                   |
| `totalDeltaB`                          | `0x06c499d8` | Replace | Read  |                   |
| `weather`                              | `0x686b6159` | Replace | Read  |                   |
| `wellOracleSnapshot`                   | `0x597490c0` | Replace | Read  |                   |

### Unripe Facet

The following `UnripeFacet` is removed from Beanstalk:

- [`0xeBD6Fc0c2d4dc3Ea131d7F14aA2f617d63Dc0F1a`](https://etherscan.io/address/0xeBD6Fc0c2d4dc3Ea131d7F14aA2f617d63Dc0F1a#code)

The following `UnripeFacet` is added to Beanstalk:

- [`0xD64BB5c2dBf12fEBeFc6397926A3c0aA6f8b6535`](https://etherscan.io/address/0xD64BB5c2dBf12fEBeFc6397926A3c0aA6f8b6535#code)

#### `UnripeFacet` Function Changes

| Name                                     | Selector     | Action  | Type  | New Functionality |
|:-----------------------------------------|:-------------|:--------|:------|:------------------|
| `getLockedBeansUnderlyingUnripeBeanEth`  | `0x208c2c98` | Remove  | Read  |                   |
| `getLockedBeansUnderlyingUnripeLP`       | `0x33f37f27` | Add     | Read  | ✓                 |
| `_getPenalizedUnderlying`                | `0xa84643e4` | Replace | Read  |                   |
| `addMigratedUnderlying`                  | `0x787cee99` | Replace | Write |                   |
| `addUnripeToken`                         | `0xfa345569` | Replace | Write |                   |
| `balanceOfPenalizedUnderlying`           | `0x1acc0a47` | Replace | Read  |                   |
| `balanceOfUnderlying`                    | `0x1be655e8` | Replace | Read  |                   |
| `chop`                                   | `0x9a516cad` | Replace | Write |                   |
| `getLockedBeans`                         | `0x087d78b4` | Replace | Read  | ✓                 |
| `getLockedBeansFromTwaReserves`          | `0x7caa025f` | Replace | Read  |                   |
| `getLockedBeansUnderlyingUnripeBean`     | `0xbfe2f3be` | Replace | Read  |                   |
| `getPenalizedUnderlying`                 | `0x6de45df2` | Replace | Read  |                   |
| `getPenalty`                             | `0x014a8a49` | Replace | Read  |                   |
| `getPercentPenalty`                      | `0xbb7de478` | Replace | Read  |                   |
| `getRecapFundedPercent`                  | `0x43cc4ee0` | Replace | Read  |                   |
| `getRecapPaidPercent`                    | `0xab434eb7` | Replace | Read  |                   |
| `getTotalUnderlying`                     | `0xadef4533` | Replace | Read  |                   |
| `getUnderlying`                          | `0x9f06b3fa` | Replace | Read  |                   |
| `getUnderlyingPerUnripeToken`            | `0xb8a04d1b` | Replace | Read  |                   |
| `getUnderlyingToken`                     | `0x691bcc88` | Replace | Read  |                   |
| `isUnripe`                               | `0xfc6a19df` | Replace | Read  |                   |
| `pick`                                   | `0x13ed3cea` | Replace | Write |                   |
| `picked`                                 | `0xd3c73ec8` | Replace | Read  |                   |
| `switchUnderlyingToken`                  | `0xa33fa99f` | Replace | Write |                   |

### Whitelist Facet

The following `WhitelistFacet` is removed from Beanstalk:

- [`0x47DA294946D41E90486ca8BB2adA493A6b974A2a`](https://etherscan.io/address/0x47DA294946D41E90486ca8BB2adA493A6b974A2a#code)

The following `WhitelistFacet` is added to Beanstalk:

- [`0xDE3a2284b50E345cB7985EE677595cC720fbBB02`](https://etherscan.io/address/0xDE3a2284b50E345cB7985EE677595cC720fbBB02#code)

#### `WhitelistFacet` Function Changes

| Name                                 | Selector     | Action   | Type  | New Functionality |
|:-------------------------------------|:-------------|:---------|:------|:------------------|
| `dewhitelistToken`                   | `0x86b40a1b` | Replace  | Write |                   |
| `getSiloTokens`                      | `0xe9522c08` | Add      | Read  |                   |
| `getWhitelistStatus`                 | `0xd9ba32fc` | Add      | Read  |                   |
| `getWhitelistStatuses`               | `0x170cf084` | Add      | Read  |                   |
| `getWhitelistedLpTokens`             | `0x9d1d2877` | Add      | Read  |                   |
| `getWhitelistedTokens`               | `0xe26f7900` | Add      | Read  |                   |
| `getWhitelistedWellLpTokens`         | `0x76a7bc84` | Add      | Read  |                   |
| `updateGaugeForToken`                | `0xce5fb821` | Add      | Write |                   |
| `updateStalkPerBdvPerSeasonForToken` | `0xf18d9ed0` | Replace  | Write |                   |
| `whitelistToken`                     | `0x371b5b03` | Add      | Write |                   |
| `whitelistTokenWithEncodeType`       | `0x052ebc26` | Add      | Write |                   |

### Event Changes

None.

## Beans Minted

None.

## Audit

### Beanstalk

The commit hash of this BIP is [45afeaf1f9c57bcdf506336aff63fa8805a1081f](https://github.com/BeanstalkFarms/Beanstalk/pull/758/commits/45afeaf1f9c57bcdf506336aff63fa8805a1081f).

An audit competition of this upgrade was held via Codehawks using commit hash [27ff8c87c9164c1fbff054be5f22e56f86cdf127](https://github.com/Cyfrin/2024-04-beanstalk-2/commit/27ff8c87c9164c1fbff054be5f22e56f86cdf127). The final report can be read [here](https://codehawks.cyfrin.io/c/2024-04-beanstalk-2/results?lt=contest&page=1&sc=reward&sj=reward&t=leaderboard).

Audit remediations were committed and documented in [PR #922](https://github.com/BeanstalkFarms/Beanstalk/pull/922). All changes were reviewed by Cyfrin.

### Basin

The commit hash of Multi Flow v1.1.0 is [8d54b04fd24ec621966f30683a90d4985c6bd6db](https://github.com/BeanstalkFarms/Basin/pull/127/commits/8d54b04fd24ec621966f30683a90d4985c6bd6db).

An audit competition of this upgrade was held via Codehawks using commit hash [038609d8adf1bf941f8abd12820bc92ecd998375](https://github.com/Cyfrin/2024-04-Beanstalk-DIB/commit/038609d8adf1bf941f8abd12820bc92ecd998375). The final report can be read [here](https://codehawks.cyfrin.io/c/2024-04-Beanstalk-DIB/results?lt=contest&page=1&sc=reward&sj=reward&t=leaderboard).

Audit remediations were committed and documented in [PR #132](https://github.com/BeanstalkFarms/Basin/pull/132). All changes were reviewed by Cyfrin.

## Effective

Immediately upon commitment.
