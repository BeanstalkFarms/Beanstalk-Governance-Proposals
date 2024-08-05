# BIP-49: Misc. Improvements

Proposed: July 29, 2024

Status: Passed

Link: [Snapshot](https://snapshot.org/#/beanstalkdao.eth/proposal/0x6d9816a73f63a122bc19d0e2690fbe744becc18dbeef2a25a39f8a48da1d44d7), [Arweave](https://arweave.net/N5GRNRgJxWNnE3yvQJnpPuek9eMbCeG0eRYSnCciN_4)

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

Proposer Wallet: [0x4a24e54a090b0fa060f7faaf561510775d314e84](https://etherscan.io/verifySig/254430)

## Summary

* Migrate the Fertilizer metadata hosted at [fert.bean.money](https://fert.bean.money/) on-chain;
* When $\Delta B_{\overline{t-1}} < 0$, change Soil issued at `gm` to be the minimum of (1) $|\Delta B|$ calculated using the instantaneous reserves from Multi Flow and (2) $|\Delta B_{\overline{t-1}}|$;
* Implement an Anti λ → λ Convert type that allows any user to decrease a Deposit's BDV if the Recorded BDV (the BDV stored on-chain with the Deposit) is greater than the Current BDV (the number of tokens in the Deposit × the current BDV of the token);
* Change the Chop Rate calculation from the [% recapitalized × % of Sprouts that have become Rinsable] to the [% recapitalized]^2;
* Update the Locked Beans calculation to account for the change to the Chop Rate; and
* Decrease the recapitalization amount upon Chop per [BIR-14](https://arweave.net/NcqX06mQ0c_FSkLxzVt1IEmWNrW7_03GXww-EsZKFq4).

## Links

* [BIP-49 GitHub PR](https://github.com/BeanstalkFarms/Beanstalk/pull/802)
* GitHub Commit Hash: [10c50916acdd1a2ea8c3699217779cbbe549389e](https://github.com/BeanstalkFarms/Beanstalk/pull/802/commits/10c50916acdd1a2ea8c3699217779cbbe549389e)
* [Safe Transaction](https://app.safe.global/transactions/tx?safe=eth:0xa9bA2C40b263843C04d344727b954A545c81D043&id=multisig_0xa9bA2C40b263843C04d344727b954A545c81D043_0x425d0779ecec50f443f71ed6a43a57121d3f159c19aa8b0599a908cd1f74807b)

## Problem

Currently, Fertilizer metadata (the SVG image, traits, etc.) is hosted on centralized infrastructure. In addition to being fundamentally misaligned with the ethos of Beanstalk, any data hosted on centralized infrastructure requires maintenance and is subject to downtime. 

Beanstalk is prone to overissuing Soil at `gm` call because Soil issuance below peg is solely based on $|\Delta B_{\overline{t-1}}|$.

Currently, the Recorded BDV of a Deposit is not necessarily equal to its Current BDV. For example, if a user Deposits BEANETH into the Silo and the USD price of ETH falls significantly (and thus the BDV of BEANETH decreases), the Recorded BDV does not decrease. In practice this means Farmers "lock in" the higher BDV at the time of Deposit such that Beanstalk is over crediting BDV to Deposits relative to their value being contributed to it. Beanstalk only supports Convert types that do not decrease the BDV of the Deposit making it impossible for the BDV associated with a Deposit to ever be corrected.

Currently, Beanstalk offers a conservative Chop Rate of ~1.33% (22.49% recapitalization * 5.9012% of debt repaid to Fertilizer) to Unripe holders. Chopping is beneficial to Beanstalk as its obligations and the amount needed to fully recapitalize Unripe assets decreases. Although the current model is likely to result in the least volatility as Beanstalk is recapitalized (as a result of Unripe holders Chopping later on average), Beanstalk can be more aggressive given its healthy position in terms of L2SR. 

Locked Beans are calculated under the extremely conservative assumption that all Unripe LP is Chopped within a single Season.

Currently, when Unripe LP holders Chop, `s.recapitalized` is unchanged as demonstrated in BIR-14. As a result, the Barn Raise ends earlier than intended, i.e., Beanstalk will not sell enough Fertilizer to fully recapitalize all Unripe LP.

## Proposed Solution

### On-Chain Fert Metadata

We propose to migrate the Fertilizer metadata hosted at [fert.bean.money](https://fert.bean.money/) on-chain.

#### Specification

* Create a new contract called `FertilizerImage.sol` to dynamically assemble the SVG image for Active, Used and Available Fertilizer; and
* Modify `uri` in `Internalizer.sol` to (1) assemble the required on-chain data, (2) generate the correct Fertilizer image URI using `imageUri` from `FertilizerImage.sol` and (3) return the final metadata URI to be consumed. 

### Soil Issuance Update

We propose to change Soil issued at `gm` when $\Delta B_{\overline{t-1}} < 0$ to be the minimum of (1) $|\Delta B|$ calculated using the instantaneous reserves from Multi Flow and (2) $|\Delta B_{\overline{t-1}}|$.

#### Specification

In `SeasonFacet/Sun.sol`:
* Implement `setSoilBelowPeg`, which calculates the cumulative deltaB across all whitelisted Wells using the instantaneous reserves in Multi Flow ($|\Delta B|$), compares it to its time weighted counterpart ($|\Delta B_{\overline{t-1}}|$) and picks the minimum of those two values; and
* Update the P < 1 case in `stepSun` to use `setSoilBelowPeg`.

### BDV Decrease

Implement a new `ANTI_LAMBDA_LAMBDA` Convert type, callable by anyone, that Converts on behalf of a Farmer and can decrease a Deposit's BDV.

#### Specification

* Add an additional parameter `account` in `LibConvert.convert(…)`;
* Add the `ANTI_LAMBDA_LAMBDA` Convert case to the Convert ladder in `LibConvert`;
* Reorder the Convert ladder in `LibConvert` so that more frequently used Converts appear first;
* To conform with previous `convertData` types, update them to return 0, and add a check that ensures proper use of `msg.sender` instead of `account` when applicable;
* In `ConvertFacet.convert(…)`, replace `msg.sender` in other Convert helper functions with `account`;
* Check if the `ANTI_LAMBDA_LAMBDA` Convert is called in `ConvertFacet.convert(…)`, and when it is, update the input Deposit with the Current BDV; and
* Pack all Convert properties in a struct called `convertParams` in `LibConvert` for readability and extensibility.

### Chop Changes

We propose to:
* Change the Chop Rate calculation from the [% recapitalized × % of Sprouts that have become Rinsable] to the [% recapitalized]^2;
* Change the Locked Beans calculation to assume that up to 75% of Unripe assets are Chopped within a single Season; and
* Decrease the recapitalization amount upon Chop per [BIR-14](https://arweave.net/NcqX06mQ0c_FSkLxzVt1IEmWNrW7_03GXww-EsZKFq4).

#### Specification

* Remove `LibUnripe.getRecapPaidPercentAmount(…)` (the percentage paid back to Fertilizer holders) from the Chop Rate calculation;
* Update `LibUnripe.getPenalizedUnderlying(…)` to calculate the Unripe λ → λ Conversion using the new formula;
* Implement `getTotalRecapitalizedPercent(…)` to get the US dollar denominated percentage recapitalized by Beanstalk;
* Update `getLockedBeansUnderlyingUnripeBean(…)` to account for the percent recapitalized percentage instead of the amount paid back to Fertilizer;
* Refactor `LibChop.chop(…)` to use `LibUnripe.removeUnderlying(…)` instead of `LibUnripe.decrementUnderlying(…)` when decreasing the available Ripe underlying amount for the Unripe token after a Chop.

## Technical Rationale

The Fertilizer Facet must be updated due to changes to the ERC-1155 token metadata. Hosting the metadata on-chain instead of on centralized infrastructure eliminates any risks associated with uptime.

The Season Facet must be updated due to changes to the deltaB calculation introduced by the Soil issuance change below peg.

The Convert Facet must be updated due to the introduction of `ANTI_LAMBDA_LAMBDA`.

The Unripe Facet must be updated due to changes to the `chop` function.

## Economic Rationale

### Soil Issuance Update

Consider an example where Beanstalk is at -300k deltaB for the first 58 minutes of a Season. At the 58th minute, i.e., 10 blocks before the next `gm` call, a Farmer buys and Sows 200k Beans, bringing the current deltaB to -100k.

Assuming no other trades in the final 2 minutes of the Season, the Soil issued at `gm` will be slightly less than 300k, despite Beanstalk only needing 100k Beans to be bought to return to peg. Before Multi Flow, this was necessary for sufficient manipulation resistance. However, thanks to Multi Flow it is now possible for Beanstalk to issue less Soil in these instances without being subject to manipulation.

In general, Beanstalk does not need to be particularly aggressive when issuing Soil—it does not want to issue debt if it doesn't have to and would prefer to spend an extra Season below peg over issuing a lot of excess Soil. 

Thus, using the inter-block MEV manipulation resistant instantaneous reserves in Multi Flow to calculate deltaB below peg is preferred.

### BDV Decrease

The ability to decrease a Deposit's BDV is necessary to prevent Beanstalk from over crediting BDV to Deposits relative to their value currently being contributed to the system. There is no incentive for a Farmer to decrease their own BDV. Thus, the Anti λ → λ Convert is callable by any user.

### Chop Changes

Beanstalk can be more aggressive given its healthy position in terms of L2SR. Chopping is beneficial to Beanstalk as its obligations and the amount needed to fully recapitalize Unripe assets decreases.

The Locked Beans calculation must be updated to account for the new Chop Rate calculation. Changing the assumption from 100% to 75% of Unripe assets Chopping within a single Season is still conservative but more realistic.

Ensuring the `chop` function is implemented as intended is essential to the structure of the Barn approved by the DAO.

## Contract Changes

### Initialization Contract

The `init` function on the following `InitBipMiscImprovements` contract is called:

- [`0x2d130cEfa0bf9D1a3e0445c9478503c326F2F8E9`](https://etherscan.io/address/0x2d130cEfa0bf9D1a3e0445c9478503c326F2F8E9#code)


### Unripe Facet

The following `UnripeFacet` is removed from Beanstalk:

- [`0xD64BB5c2dBf12fEBeFc6397926A3c0aA6f8b6535`](https://etherscan.io/address/0xD64BB5c2dBf12fEBeFc6397926A3c0aA6f8b6535#code)

The following `UnripeFacet` is added to Beanstalk:

- [`0xb52af7889b05eF652468E98D249700415a9F587D`](https://etherscan.io/address/0xb52af7889b05eF652468E98D249700415a9F587D#code)

#### `UnripeFacet` Function Changes

| Name                                     | Selector     | Action  | Type  | New Functionality |
|:-----------------------------------------|:-------------|:--------|:------|:------------------|
| `_getPenalizedUnderlying`                | `0xa84643e4` | Remove  | Read  |                   |
| `getRecapitalized`                       | `0xe68a543a` | Add     | Read  | ✓                 |
| `addMigratedUnderlying`                  | `0x787cee99` | Replace | Write |                   |
| `addUnripeToken`                         | `0xfa345569` | Replace | Write |                   |
| `balanceOfPenalizedUnderlying`           | `0x1acc0a47` | Replace | Read  |                   |
| `balanceOfUnderlying`                    | `0x1be655e8` | Replace | Read  |                   |
| `chop`                                   | `0x9a516cad` | Replace | Write |                   |
| `getLockedBeans`                         | `0x087d78b4` | Replace | Read  |                   |
| `getLockedBeansFromTwaReserves`          | `0x7caa025f` | Replace | Read  |                   |
| `getLockedBeansUnderlyingUnripeBean`     | `0xbfe2f3be` | Replace | Read  | ✓                 |
| `getLockedBeansUnderlyingUnripeLP`       | `0x33f37f27` | Replace | Read  |                   |
| `getPenalizedUnderlying`                 | `0x6de45df2` | Replace | Read  |                   |
| `getPenalty`                             | `0x014a8a49` | Replace | Read  |                   |
| `getPercentPenalty`                      | `0xbb7de478` | Replace | Read  | ✓                 |
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

### Convert Facet

The following `ConvertFacet` is removed from Beanstalk:

- [`0xEb1b833D3E81cb3d390514cabB9b809E6170626C`](https://etherscan.io/address/0xEb1b833D3E81cb3d390514cabB9b809E6170626C#code)

The following `ConvertFacet` is added to Beanstalk:

- [`0x2604D728D8c6918b8C427bF42aa2ed07ceB5cf23`](https://etherscan.io/address/0x2604D728D8c6918b8C427bF42aa2ed07ceB5cf23#code)

#### `ConvertFacet` Function Changes

| Name                  | Selector     | Action   | Type  | New Functionality |
|:----------------------|:-------------|:---------|:------|:------------------|
| `convert`             | `0xb362a6e8` | Replace  | Write | ✓                 |

### Convert Getters Facet

The following `ConvertGettersFacet` is removed from Beanstalk:

- [`0x8ABa09526dc6EB6Ea44eE3f8745dD8bc9EF744E2`](https://etherscan.io/address/0x8ABa09526dc6EB6Ea44eE3f8745dD8bc9EF744E2#code)

The following `ConvertGettersFacet` is added to Beanstalk:

- [`0x09D466663586292F5f0c1D99e9547A7A81E887f5`](https://etherscan.io/address/0x09D466663586292F5f0c1D99e9547A7A81E887f5#code)

#### `ConvertGettersFacet` Function Changes

| Name                  | Selector     | Action   | Type | New Functionality |
|:----------------------|:-------------|:---------|:-----|:------------------|
| `getAmountOut`        | `0x4aa06652` | Replace  | Read |                   |
| `getMaxAmountIn`      | `0x24dd285c` | Replace  | Read |                   |

### Season Facet

The following `SeasonFacet` is removed from Beanstalk:

- [`0x92458b7ade7798c45E5ff583c353F70F950d66Cf`](https://etherscan.io/address/0x92458b7ade7798c45E5ff583c353F70F950d66Cf#code)

The following `SeasonFacet` is added to Beanstalk:

- [`0x51F74C024936E296132c6F4E59b9EEd677eb3B1c`](https://etherscan.io/address/0x51F74C024936E296132c6F4E59b9EEd677eb3B1c#code)

#### `SeasonFacet` Function Changes

| Name                 | Selector     | Action  | Type  | New Functionality |
|:---------------------|:-------------|:--------|:------|:------------------|
| `gm`                 | `0x64ee4b80` | Replace | Write | ✓                 |
| `seasonTime`         | `0xca7b7d7b` | Replace | Read  |                   |
| `sunrise`            | `0xfc06d2a6` | Replace | Write |                   |

### Season Getters Facet

The following `SeasonGettersFacet` is removed from Beanstalk:

- [`0x5A1675f3156c9e73D7eA20eb58470A0002865E85`](https://etherscan.io/address/0x5A1675f3156c9e73D7eA20eb58470A0002865E85#code)

The following `SeasonGettersFacet` is added to Beanstalk:

- [`0x9fd4daD032324bC569decC3795731aEC309c2923`](https://etherscan.io/address/0x9fd4daD032324bC569decC3795731aEC309c2923#code)

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
| `totalDeltaB`                          | `0x06c499d8` | Replace | Read  | ✓                 |
| `weather`                              | `0x686b6159` | Replace | Read  |                   |
| `wellOracleSnapshot`                   | `0x597490c0` | Replace | Read  |                   |

### Fertilizer Facet

The following `FertilizerFacet` is removed from Beanstalk:

- [`0x729672c68134E2DF0CdD36D3296841A2993534c7`](https://etherscan.io/address/0x729672c68134E2DF0CdD36D3296841A2993534c7#code)

The following `FertilizerFacet` is added to Beanstalk:

- [`0x9586878Ea437CBB6D4b6381Bde7B064f531CfB77`](https://etherscan.io/address/0x9586878Ea437CBB6D4b6381Bde7B064f531CfB77#code)

#### `FertilizerFacet` Function Changes

| Name                         | Selector     | Action  | Type  | New Functionality |
|:-----------------------------|:-------------|:--------|:------|:------------------|
| `getTotalRecapDollarsNeeded` | `0x12cb5eab` | Add     | Read  | ✓                 |
| `_getMintFertilizerOut`      | `0x94daa221` | Replace | Read  |                   |
| `balanceOfBatchFertilizer`   | `0x304ec65d` | Replace | Read  |                   |
| `balanceOfFertilized`        | `0xb6f42085` | Replace | Read  |                   |
| `balanceOfFertilizer`        | `0x1799b3b2` | Replace | Read  |                   |
| `balanceOfUnfertilized`      | `0x1edb6be1` | Replace | Read  |                   |
| `beansPerFertilizer`         | `0x9bb4e35a` | Replace | Read  |                   |
| `beginBarnRaiseMigration`    | `0xe3d4e44c` | Replace | Write |                   |
| `claimFertilized`            | `0x83e08888` | Replace | Write |                   |
| `getActiveFertilizer`        | `0xdc6ba285` | Replace | Read  |                   |
| `getBarnRaiseToken`          | `0xf255da60` | Replace | Read  |                   |
| `getBarnRaiseWell`           | `0x93a39bea` | Replace | Read  |                   |
| `getCurrentHumidity`         | `0x39448802` | Replace | Read  |                   |
| `getEndBpf`                  | `0xc85951a1` | Replace | Read  |                   |
| `getFertilizer`              | `0x9c45a1d5` | Replace | Read  |                   |
| `getFertilizers`             | `0x34af5416` | Replace | Read  |                   |
| `getFirst`                   | `0x1e223143` | Replace | Read  |                   |
| `getHumidity`                | `0x29130a66` | Replace | Read  |                   |
| `getLast`                    | `0x4d622831` | Replace | Read  |                   |
| `getMintFertilizerOut`       | `0x69744dd0` | Replace | Read  |                   |
| `getNext`                    | `0xf4a057e2` | Replace | Read  |                   |
| `isFertilizing`              | `0x6ae1c014` | Replace | Read  |                   |
| `mintFertilizer`             | `0x363591d0` | Replace | Write |                   |
| `payFertilizer`              | `0xd47aee59` | Replace | Write |                   |
| `remainingRecapitalization`  | `0x4a16607c` | Replace | Read  |                   |
| `totalFertilizedBeans`       | `0x4f9a9678` | Replace | Read  |                   |
| `totalFertilizerBeans`       | `0xf9c4ebde` | Replace | Read  |                   |
| `totalUnfertilizedBeans`     | `0xa3ef48c9` | Replace | Read  |                   |

### Event Changes

None.

## Beans Minted

None.

## Audit

The commit hash of this BIP is [10c50916acdd1a2ea8c3699217779cbbe549389e](https://github.com/BeanstalkFarms/Beanstalk/pull/802/commits/10c50916acdd1a2ea8c3699217779cbbe549389e).

An audit competition of this upgrade was held via Codehawks using commit hash [662d26f12ee219ee92dc485c06e01a4cb5ee8dfb](https://github.com/Cyfrin/2024-05-Beanstalk-3/commit/662d26f12ee219ee92dc485c06e01a4cb5ee8dfb). The final report can be read [here](https://codehawks.cyfrin.io/c/2024-05-Beanstalk-3/results?lt=contest&page=1&sc=reward&sj=reward&t=leaderboard).

Audit remediations were committed and documented in [PR #943](https://github.com/BeanstalkFarms/Beanstalk/pull/934). All changes were reviewed by Cyfrin.

### Post Audit Changes

The following changes have been made to the BIP-49 code, but have not been audited:
* Updated the constants in `LibLockedUnderlying` to reflect the change in the Locked Beans calculation;
* Updated the Beans per Fertilizer remaining calculation in `Fertilizer/Internalizer.sol` used by the Fertilizer SVGs; and
* Updated `SeasonGettersFacet.getTotalDeltaB()` to calculate the cumulative deltaB across all whitelisted Wells.

## Effective

Immediately upon commitment.
