# EBIP-19: Misc Bug Fixes 2

Committed: October 13, 2024

---

- [Submitter](#submitter)
- [Summary](#summary)
- [Links](#links)
- [Problem](#problem)
- [Solution](#solution)
- [Contract Changes](#contract-changes)
- [Beans Minted](#beans-minted)
- [Effective](#effective)

## Submitter

Beanstalk Community Multisig

## Summary

* Update Morning Auction and Generalized Convert to fetch the L2 block number;
* Update `getAmountOut`  to return the correct value when Converting Unripe LP to Unripe Bean;
* Upgrade the Gauge System to update Gauge Points and Seeds for whitelisted LP tokens regardless of the number of Beans in each Well;
* Update the whitelisted Wells to use Multi Flow Pump v1.2.1, which properly resets the reserves in the Well when the reserves go from non-zero to zero; and
* Update the whitelisted Wells that use the Constant Product 2 Well Function to use Constant Product 2 v1.2.1, which uses the `Math` library to increase precision in operations in the `calcRate` function.

## Links

Per the process outlined in the [BCM Emergency Response Procedures](https://docs.bean.money/almanac/governance/beanstalk/bcm-process#emergency-response-procedures), the BCM can take swift action to protect Beanstalk in the event of a bug or security vulnerability.

- [EBIP-19 GitHub PR](https://github.com/BeanstalkFarms/Beanstalk/pull/1148)
- GitHub Commit Hash: [a26664dd64ca39bdc1db98355736363f29a9fc06](https://github.com/BeanstalkFarms/Beanstalk/tree/a26664dd64ca39bdc1db98355736363f29a9fc06)
- [Safe Transaction](https://app.safe.global/transactions/tx?safe=arb1:0xDd5b31E73dB1c566Ca09e1F1f74Df34913DaaF69&id=multisig_0xDd5b31E73dB1c566Ca09e1F1f74Df34913DaaF69_0x689c0612f67ac3f7e0e1ceb55eb689a4ba4b5dc9926b62d46c3db10295e474a2)
- [Etherscan Transaction](https://arbiscan.io/tx/0x83f77c3b44e76d3cd23472fc17da4641eadb35e270108ea3e2d9b58ac5c17765)
- [Arweave](https://arweave.net/ykNMX6ZzhuT_M4FaCbxPV3do87V3RPAYBoPxO09U5lI)

## Problem

The Morning Auction and Generalized Convert used `block.number` to determine how much time had passed and Convert capacity (the amount of assets that can be Converted within 1 L2 block), respectively. On Arbitrum, `block.number` returns the L1 block number rather than the latest block number on Arbitrum. As a result, the Morning Auction had been lasting longer than its intended duration (the entire Season) and the Convert capacity was accrued over multiple blocks rather a single block.

The `getAmountOut` view function is used in order to return the amount of tokens returned from a Convert. This function returned an incorrect value when attempting to fetch the amount returned from Converting Unripe LP to Unripe Bean.

Previously, if a whitelisted Well had fewer than 1000 Beans, Beanstalk assumed manipulation was occurring in the Well. When this manipulation was detected, the LP Gauge System did not change the Gauge Points nor Seeds of any whitelisted LP token. This creates liveness issues when new Wells are whitelisted, such as in [BIP-50](https://arweave.net/ciB1dygmXmEjLSOWv5P1NhAf-4BhOcMCH1ZfJw5JmMs).

The Multi Flow Pump v1.2 (deployed for BIP-50) stored incorrect reserves when a Well went from non-zero to zero reserves.

The `calcRate` function in the Constant Product 2 Well Function failed when reserves were set to the max value.

No funds were at risk as a result of these bugs.

## Solution

Update Morning Auction and Generalized Convert to fetch the L2 block number.

Update `getAmountOut`  to return the correct value when Converting Unripe LP to Unripe Bean.

Upgrade the Gauge System to update Gauge Points and Seeds for whitelisted LP tokens regardless of the number of Beans in each Well.

Update the whitelisted Wells to use Multi Flow Pump v1.2.1, which properly resets the reserves in the Well when the reserves go from non-zero to zero.

Update the whitelisted Wells that use the Constant Product 2 Well Function to use Constant Product 2 v1.2.1, which uses the `Math` library to increase precision in operations in the `calcRate` function.

## Contract Changes

### Initialization Contract

The `init` function on the following `InitMultiFlowPumpUpgrade` contract is called:

- [`0xF48B9F669EB7b40e46CDDCf04B1c6f07E2458C1F`](https://arbiscan.io/address/0xF48B9F669EB7b40e46CDDCf04B1c6f07E2458C1F#code)

### Season Facet

The following `SeasonFacet` is being removed from Beanstalk:

- [`0x40c8688969c91290311314fbB2f10156b43Fbe4B`](https://arbiscan.io/address/0x40c8688969c91290311314fbB2f10156b43Fbe4B#code)

The following `SeasonFacet` is added to Beanstalk:

- [`0x64504c8b23350FEEebd5BB978633C0CfFfb9D536`](https://arbiscan.io/address/0x64504c8b23350FEEebd5BB978633C0CfFfb9D536#code)

#### `SeasonFacet` Function Changes

| Name                                   | Selector     | Action    | Type  | New Functionality |
|:---------------------------------------|:-------------|:----------|:------|:------------------|
| `getShipmentRoutes`                    | `0xfd497a68` | Replace   | Write |                   |
| `setShipmentRoutes`                    | `0xf1e2dfb0` | Replace   | Read  |                   |
| `gm`                                   | `0x64ee4b80` | Replace   | Write |  ✓                |
| `seasonTime`                           | `0xca7b7d7b` | Replace   | Read  |                   |
| `sunrise`                              | `0xfc06d2a6` | Replace   | Write |  ✓                |

### Season Getters Facet

The following `SeasonGettersFacet` is being removed from Beanstalk:

- [`0xfe15fe467d06Ce19d20709eAE9E24B3bD8309132`](https://arbiscan.io/address/0xfe15fe467d06Ce19d20709eAE9E24B3bD8309132#code)

The following `SeasonGettersFacet` is added to Beanstalk:

- [`0xdB9882aaAC47Fc1D62942951311704FDDf531ebd`](https://arbiscan.io/address/0xdB9882aaAC47Fc1D62942951311704FDDf531ebd#code)

#### `SeasonGettersFacet` Function Changes

| Name                                     | Selector     | Action    | Type  | New Functionality |
|:-----------------------------------------|:-------------|:----------|:------|:------------------|
| `cumulativeCurrentDeltaB`                | `0x89a218d2` | Replace   | Read  |                   |
| `getAbsBeanToMaxLpRatioChangeFromCaseId` | `0xe53b479e` | Replace   | Read  |                   |
| `getAbsTemperatureChangeFromCaseId`      | `0x3cee5dea` | Replace   | Read  |                   |
| `getCaseData`                            | `0x8097f0ca` | Replace   | Read  |                   |
| `getCases`                               | `0x065cb594` | Replace   | Read  |                   |
| `getChangeFromCaseId`                    | `0x43e0156a` | Replace   | Read  |                   |
| `getDeltaPodDemandLowerBound`            | `0x57801d87` | Replace   | Read  |                   |
| `getDeltaPodDemandUpperBound`            | `0x70fd1b06` | Replace   | Read  |                   |
| `getEvaluationParameters`                | `0xda61af62` | Replace   | Read  |                   |
| `getExcessivePriceThreshold`             | `0x44fb7cc3` | Replace   | Read  |                   |
| `getLpToSupplyRatioUpperBound`           | `0x1eedbfbb` | Replace   | Read  |                   |
| `getLpToSupplyRatioOptimal`              | `0x1f48a553` | Replace   | Read  |                   |
| `getLpToSupplyRatioLowerBound`           | `0x11a8d895` | Replace   | Read  |                   |
| `getMaxBeanMaxLpGpPerBdvRatio`           | `0xab843b34` | Replace   | Read  |                   |
| `getMinBeanMaxLpGpPerBdvRatio`           | `0xb3c39ce5` | Replace   | Read  |                   |
| `getPodRateLowerBound`                   | `0xfd6d1483` | Replace   | Read  |                   |
| `getPodRateOptimal`                      | `0xdd9330d2` | Replace   | Read  |                   |
| `getPodRateUpperBound`                   | `0x08fa96d3` | Replace   | Read  |                   |
| `getRelBeanToMaxLpRatioChangeFromCaseId` | `0x35870a7a` | Replace   | Read  |                   |
| `getRelTemperatureChangeFromCaseId`      | `0x4d65f762` | Replace   | Read  |                   |
| `getSeasonStruct`                        | `0x738ad142` | Replace   | Read  |                   |
| `getSeasonTimestamp`                     | `0xf07f0760` | Replace   | Read  |                   |
| `getTargetSeasonsToCatchUp`              | `0xcb677411` | Replace   | Read  |                   |
| `getWellsByDeltaB`                       | `0xbf170533` | Replace   | Read  |                   |
| `poolCurrentDeltaB`                      | `0x8223eac8` | Replace   | Read  |                   |
| `abovePeg`                               | `0x2a27c499` | Replace   | Read  |                   |
| `getLargestLiqWell`                      | `0xd1943f7f` | Replace   | Read  |                   |
| `getTotalUsdLiquidity`                   | `0xbbf459a7` | Replace   | Read  |                   |
| `getTotalWeightedUsdLiquidity`           | `0xf788b47c` | Replace   | Read  |                   |
| `getTwaLiquidityForWell`                 | `0xa13a3742` | Replace   | Read  |                   |
| `getWeightedTwaLiquidityForWell`         | `0x93c9e531` | Replace   | Read  |                   |
| `l2BlockNumber`                          | `0x8b85902b` | Replace   | Read  |   ✓               |
| `paused`                                 | `0x5c975abb` | Replace   | Read  |                   |
| `plentyPerRoot`                          | `0x3fccd20c` | Replace   | Read  |                   |
| `poolDeltaB`                             | `0x471bcdbe` | Replace   | Read  |                   |
| `rain`                                   | `0x43def26e` | Replace   | Read  |                   |
| `season`                                 | `0xc50b0fb0` | Replace   | Read  |                   |
| `sunriseBlock`                           | `0x3b2ecb70` | Replace   | Read  |                   |
| `time`                                   | `0x16ada547` | Replace   | Read  |                   |
| `totalDeltaB`                            | `0x06c499d8` | Replace   | Read  |                   |
| `weather`                                | `0x686b6159` | Replace   | Read  |                   |
| `wellOracleSnapshot`                     | `0x597490c0` | Replace   | Read  |                   |

### Convert Facet

The following `ConvertFacet` is being removed from Beanstalk:

- [`0x242A339C73d3b373a91C157865B36a1480ec3b09`](https://arbiscan.io/address/0x242A339C73d3b373a91C157865B36a1480ec3b09#code)

The following `ConvertFacet` is added to Beanstalk:

- [`0xcE333Cc99C477A7d96FDe73905e0B3576e86b321`](https://arbiscan.io/address/0xcE333Cc99C477A7d96FDe73905e0B3576e86b321#code)

#### `ConvertFacet` Function Changes

| Name                                   | Selector     | Action  | Type  | New Functionality |
|:---------------------------------------|:-------------|:--------|:------|:------------------|
| `convert`                              | `0xb362a6e8` | Add     | Write |  ✓                |

### Convert Getters Facet

The following `ConvertGettersFacet` is being removed from Beanstalk:

- [`0x999A04B54a386b1C68A9Be926AF0200F2C49A47A`](https://arbiscan.io/address/0x999A04B54a386b1C68A9Be926AF0200F2C49A47A#code)

The following `ConvertGettersFacet` is added to Beanstalk:

- [`0x6eF9cC52Eb37e0dE9592960c0c894a1000ac7dDf`](https://arbiscan.io/address/0x6eF9cC52Eb37e0dE9592960c0c894a1000ac7dDf#code)

#### `ConvertGettersFacet` Function Changes

| Name                                   | Selector     | Action    | Type  | New Functionality |
|:---------------------------------------|:-------------|:----------|:------|:------------------|
| `calculateDeltaBFromReserves`          | `0xd052f0d5` | Replace   | Read  |                   |
| `calculateStalkPenalty`                | `0xb325d2ef` | Replace   | Read  |                   |
| `cappedReservesDeltaB`                 | `0x6842f2b3` | Replace   | Read  |                   |
| `getAmountOut`                         | `0x4aa06652` | Replace   | Read  |                   |
| `getMaxAmountIn`                       | `0x24dd285c` | Replace   | Read  |                   |
| `getOverallConvertCapacity`            | `0xf66d5589` | Replace   | Read  |  ✓                |
| `getWellConvertCapacity`               | `0xb905065b` | Replace   | Read  |  ✓                |
| `getUsedConvertCapacity`               | `0xb8ff0f01` | Add       | Read  |  ✓                |
| `overallCappedDeltaB`                  | `0x3e8b56f1` | Replace   | Read  |                   |
| `overallCurrentDeltaB`                 | `0xb267ea07` | Replace   | Read  |                   |
| `scaledDeltaB`                         | `0x24568abf` | Replace   | Read  |                   |

### Pipeline Convert Facet

The following `PipelineConvertFacet` is being removed from Beanstalk:

- [`0x6B1B5E5cef71f0cC65d32B67D8794F58faD491a3`](https://arbiscan.io/address/0x6B1B5E5cef71f0cC65d32B67D8794F58faD491a3#code)

The following `PipelineConvertFacet` is added to Beanstalk:

- [`0xA047EAD9719dE8B030a10dd3d152518db7cf0099`](https://arbiscan.io/address/0xA047EAD9719dE8B030a10dd3d152518db7cf0099#code)

#### `PipelineConvertFacet` Function Changes

| Name                                   | Selector     | Action    | Type  | New Functionality |
|:---------------------------------------|:-------------|:----------|:------|:------------------|
| `pipelineConvert`                      | `0xbb25c1c2` | Replace   | Write |   ✓               |

### Field Facet

The following `FieldFacet` is being removed from Beanstalk:

- [`0xa9085918d5632EA12BA91709F819B800fa8B3726`](https://arbiscan.io/address/0xa9085918d5632EA12BA91709F819B800fa8B3726#code)

The following `FieldFacet` is added to Beanstalk:

- [`0xa80A1F63bb8d5cece044E4e12480dB3930E9885C`](https://arbiscan.io/address/0xa80A1F63bb8d5cece044E4e12480dB3930E9885C#code)

#### `FieldFacet` Function Changes

| Name                                   | Selector     | Action    | Type  | New Functionality |
|:---------------------------------------|:-------------|:----------|:------|:------------------|
| `activeField`                          | `0xd1eba544` | Replace   | Read  |                   |
| `addField`                             | `0xb94e871c` | Replace   | Write |                   |
| `balanceOfPods`                        | `0x9a337c1d` | Replace   | Read  |                   |
| `fieldCount`                           | `0xbb485bbd` | Replace   | Read  |                   |
| `getPlotIndexesFromAccount`            | `0x253fcfb5` | Replace   | Read  |                   |
| `getPlotsFromAccount`                  | `0x91b24284` | Replace   | Read  |                   |
| `isHarvesting`                         | `0x4bea67c4` | Replace   | Read  |                   |
| `setActiveField`                       | `0x057c571b` | Replace   | Write |                   |
| `totalHarvestableForActiveField`       | `0x237dbac5` | Replace   | Read  |                   |
| `harvest`                              | `0xe9bbb033` | Replace   | Write |                   |
| `harvestableIndex`                     | `0xb511654d` | Replace   | Read  |                   |
| `maxTemperature`                       | `0x7907091f` | Replace   | Read  |                   |
| `plot`                                 | `0x9ee7ea12` | Replace   | Read  |                   |
| `podIndex`                             | `0xccda40b9` | Replace   | Read  |                   |
| `remainingPods`                        | `0x56ba3e24` | Replace   | Read  |                   |
| `sow`                                  | `0x32ab68ce` | Replace   | Write |                   |
| `sowWithMin`                           | `0x553030d0` | Replace   | Write |                   |
| `temperature`                          | `0xadccea12` | Replace   | Read  |                   |
| `totalHarvestable`                     | `0x2e76f597` | Replace   | Read  |                   |
| `totalHarvested`                       | `0x352525a6` | Replace   | Read  |                   |
| `totalPods`                            | `0xf1e0a211` | Replace   | Read  |                   |
| `totalSoil`                            | `0x3285008a` | Replace   | Read  |                   |
| `totalUnharvestable`                   | `0xf29ffe94` | Replace   | Read  |                   |

## Beans Minted

None.

## Effective

Effective immediately upon commitment, which has already happened.
