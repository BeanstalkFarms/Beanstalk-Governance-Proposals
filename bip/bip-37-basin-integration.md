# BIP-37: Basin Integration

Proposed: August 23, 2023

Status: Passed

Link: [Snapshot](https://snapshot.org/#/beanstalkdao.eth/proposal/0x98fbf0e9a3fe3054679fefb544fd57cd4df1f85b03edeecd1a06ccefdd4d1def), [Arweave](https://arweave.net/VPF1EafHb41Y7AjzUYB0j-mR8Sl0KxPknDJYffczBpc)

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

Brendan Sanderson, Ben Weintraub, Beanstalk Farms

Proposer Wallet: [0xf9d183af486a973b7921ceb5fdc9908d12aab440](https://etherscan.io/verifySig/22706)

## Summary

* Provide infrastructure for any Well LP token from the [Basin DEX](https://basin.exchange/basin.pdf) to be integrated into Beanstalk;
* Add the BEAN:ETH Constant Product Well LP token (BEANETH) to the Deposit Whitelist with 1 Stalk per BDV and 4.5 Seeds per BDV;
    * Support Conversions from Beans to BEANETH above peg and vice versa below peg; and
    * Add the BEANETH Well to the Oracle Whitelist;
* Change the Bean and BEAN:3CRV Curve LP token (BEAN3CRV) Seeds per BDV to 3 Seeds and 3.25 Seeds, respectively;
* Add view functions for getting account level BDV and last Mowed Stems to the Silo;
* Upgrade the Depot Facet to use [Pipeline v1.0.1](https://github.com/BeanstalkFarms/Pipeline#v101-current);
* Use the BEANETH Well to calculate the award for successfully calling the `gm` function;
* Update the Flood implementation to reflect the removal of the Withdrawal Freeze;
* Update the [Beanstalk DAO Disclosures](https://bean.money/disclosures) to reflect the state of Beanstalk and its risks; and
* Update the list of Beanstalk Farms Multisig (BFM) signers and Beanstalk Immunefi Committee (BIC) members.

## Links

* [BIP-37 GitHub PR](https://github.com/BeanstalkFarms/Beanstalk/pull/378)
* GitHub Commit Hash: [76a188233917afc1db103ca64b02dab8c5280b35](https://github.com/BeanstalkFarms/Beanstalk/tree/76a188233917afc1db103ca64b02dab8c5280b35)
* [Safe Transaction](https://app.safe.global/transactions/tx?safe=eth:0xa9bA2C40b263843C04d344727b954A545c81D043&id=multisig_0xa9bA2C40b263843C04d344727b954A545c81D043_0x7ac542c48b5e020bc5c26667651861a4542d49150b4be3a2f8b4e011b7feb5db)

## Problem

### Basin Integration

Basin is a composable EVM-native decentralized exchange protocol. Currently there is no generalized way for [Wells](https://basin.exchange/basin.pdf#subsection.3.1) (pools of the Basin DEX) to be easily integrated into the Silo (*i.e.*, the Deposit, Convert and Oracle Whitelists).

### Whitelist BEANETH

Currently Beans only trade against 3CRV, which is a significant centralization vector for Beanstalk. Additionally, the BEAN:3CRV pool, which is currently the sole oracle for calculating the Bean price, is not multi-block MEV manipulation resistant.

### Seeds per BDV Changes

The Seeds per BDV of Bean and BEAN3CRV can be optimized given (1) the new option for providing Bean liquidity via the BEANETH Well and (2) that Unripe Depositors had accumulated Grown Stalk for about a year from Replant to the commitment of Silo V3. As a result of (2), the average Grown Stalk per BDV is excessively high for older Deposits, compared to newer ones.

### Silo V3 Getters

Currently there is no way to get the total Deposited BDV or the last Mowed Stems for a particular Farmer.

### Pipeline Upgrade 

Pipeline v1.0.0 is unable to receive transfers of Ether, which makes it difficult to unwrap WETH as a part of a Pipeline function call. Pipeline v1.0.1 fixes this issue, but currently the Depot Facet uses Pipeline v1.0.0. 

### `gm` Incentive Reward Oracle

Currently Beanstalk uses the ratio between the USDC:ETH Uniswap V3 and BEAN:3CRV Curve pools to calculate the cost of executing the `gm` function in Beans. Both pools are subject to multi-block MEV manipulation and calculating the ratio between two pools is gas-inefficient. 

### Flood Update

Currently the implementation of Flood is not functional after the removal of the Withdrawal Freeze in [BIP-36](https://arweave.net/rSUoGXf3bJPVUZAixhmMtu5sGl6fiNcJ935NO6VMFWE).

### Disclosures

The Beanstalk DAO Disclosures can be updated to reflect the current state of Beanstalk and its risks.

## Proposed Solution

### Basin Integration

Provide the infrastructure for any Well LP token to be integrated into Beanstalk:
* Add a Well LP token BDV function (`wellBdv`) that permits any Well LP token to be added to the Deposit Whitelist. Implemented in `BDVFacet` and `LibWellBdv`.
* Upgrade the Deposit Whitelist to include an `encodeType`, which allows for BDV functions with different function signatures to be properly encoded. Currently, `function(uint256)` (`encodeType` = 0) and `function(address, uint256)` (`encodeType` = 1) are supported. The generalized Well BDV function uses `encodeType = 1`.
* Add the `convertLPToBeans` and `convertBeansToLP` functions to Convert to peg from Beans to any whitelisted Well LP token and vice versa in `LibWellConvert`. Also, add the `BEANS_TO_WELL_LP` and `WELL_LP_TO_BEANS`  Convert types to access the new Convert methods.
* Add a generalized Well minting oracle to capture the time weighted average deltaB in a Well containing Beans in `LibWellMinting`.

Move `enrootDeposit(s)` from `ConvertFacet` to a separate `EnrootFacet`.

Split up `ConvertFacet` into `ConvertFacet` and `ConvertGettersFacet`.

### Whitelist BEANETH

Add BEANETH to the Deposit Whitelist with 1 Stalk per BDV and 4.5 Seeds per BDV. Also, support Conversions from Beans to BEANETH above peg and from BEANETH to Beans below peg.

In order for Beanstalk to calculate deltaB in the BEANETH Well, it must compare the manipulation resistant BEAN:ETH price in the Well with a manipulation resistant ETH:USD price.

Pumps are the generalized oracle component of Basin. [Multi Flow](https://basin.exchange/multi-flow-pump.pdf) is the Pump that is attached to the BEANETH Well and provides a multi-block MEV manipulation resistant BEAN:ETH price.

`LibEthUsdOracle` contains functionalty to fetch a manipulation resistant ETH:USD price. `LibEthUsdOracle` uses a greedy algorithm to return a gas efficient average price of (1) the current price returned by the ETH/USD Chainlink data feed and (2) either the ETH:USDC Uniswap V3 pool or the ETH:USDT Uniswap V3 pool depending on which is closer to (1). If the ETH:USDC Uniswap V3 price is sufficiently close (0.3%) to the Chainlink data feed price, then the oracle will not check the ETH:USDT Uniswap V3 price to save gas (*i.e.*, be greedy). The oracle only returns a value if the price difference between the ETH/USD Chainlink data feed and either Uniswap V3 pool is less than 1%.

The BEANETH Well is added to the Oracle Whitelist by including the Well in the `stepOracle` and `totalDeltaB` functions in `SeasonFacet/Oracle.sol`.

Minting based on the BEANETH Well is subject to the 1% deltaB cap implemented in [EBIP-2](https://bean.money/ebip-2). With 2 pools on the Oracle Whitelist, the maximum amount that the Bean supply can increase each Season is 2% per the peg maintenance mechanism.

Update `MetadataImage` to include the BEANETH token and add the `name` and `symbol` functions to `MetadataFacet` to integrate Deposits with OpenSea.

### Seeds per BDV Changes

Set the Seeds per BDV for Bean and BEAN3CRV to 3 Seeds and 3.25 Seeds, respectively.

After the commitment of this upgrade, the Deposit Whitelist would be the following:

| Whitelisted asset    | Stalk per BDV    | Seeds per BDV    |
|:---------------------|:-----------------|:-----------------|
| Bean                 | 1                | 3                |
| BEAN:3CRV Curve LP   | 1                | 3.25             |
| BEAN:ETH Well LP     | 1                | 4.5              |
| Unripe Bean          | 1                | 0                |
| Unripe BEAN:3CRV LP  | 1                | 0                |

### Silo V3 Getters

Add the following functions to the Silo Facet that return Deposited BDV and last Mowed Stem data for a given Farmer:
* `balanceOfDepositedBDV`;
* `getLastMowedStem`; and
* `getMowStatus`.

### Pipeline Upgrade 

Upgrade the Depot Facet to use Pipeline v1.0.1 ([`0xb1bE0000C6B3C62749b5F0c92480146452D15423`](https://etherscan.io/address/0xb1bE0000C6B3C62749b5F0c92480146452D15423)). All updates were reviewed by Halborn.

#### Pipeline v1.0.1 Changelog
- Add `receive` function fallback; and
- Add `version` function.

### `gm` Incentive Reward Oracle

Upgrade the `gm` incentive reward oracle to calculate the cost to execute the `gm` function in Beans using the BEANETH Well.

Use `getBeanEthWellPrice` in `LibBeanEthWellOracle` to calculate the price of Ether in Beans. Remove the `getBeanUsdPrice` and `getEthUsdcPrice` functions in `LibIncentive` that were previously used to calculate the price of Ether in Beans using the ratio of the two return values.

### Flood Update

Update `handleRain` in `SeasonFacet/Weather.sol` to reflect the removal of the Withdrawal Freeze. Beanstalk waits 1 Season after it starts Raining before Flooding.

### Disclosures

The following is a list of proposed changes to the Beanstalk DAO Disclosures:

* Add the following disclosure about Basin:
    
    **A VULNERABILITY IN BASIN OR ITS COMPONENTS COULD RESULT IN A LOSS OF FUNDS. BEANSTALK ASSUMES THE SECURITY OF BASIN AND ITS CORRESPONDING COMPONENTS.**

    Beans trade in the BEAN:ETH Well on Basin. The LP token is whitelisted in the Silo and the Well is used by Beanstalk to determine how many Beans and/or Soil to mint. Basin and the corresponding components that Beanstalk uses (the Constant Product Well Function, the Well Implementation, Multi Flow, etc.) were audited by Halborn, Cyfrin and Code4rena. While all are reputable auditors, there is no guarantee that Basin or its components are secure. Beanstalk assumes the security of Basin and its corresponding components.
    
* Add the following disclosure about Uniswap V3:

    **A VULNERABILITY IN UNISWAP V3 COULD RESULT IN A LOSS OF FUNDS. BEANSTALK ASSUMES THE SECURITY OF UNISWAP V3.**
    
    Beanstalk uses the ETH:USDC and ETH:USDT Uniswap V3 pools to derive the price of a dollar on-chain. Uniswap V3 is among the largest Ethereum-native decentralized exchange protocols by volume. In general, open source protocols with large amounts of value on them and long track records indicate security, but there is no guarantee. Beanstalk assumes the security of Uniswap V3.
    
* Add the following disclosure about Pipeline:

    **A VULNERABILITY IN PIPELINE COULD RESULT IN A LOSS OF FUNDS. BEANSTALK ASSUMES THE SECURITY OF PIPELINE.**
    
    Through Beanstalk, users can perform complex, gas-efficient interactions with other Ethereum-native protocols, like Pipeline. Pipeline is a sandbox contract allows anyone to perform an arbitrary series of actions in the EVM in a single transaction.
    
    Pipeline was audited by Halborn, but there is no guarantee that Pipeline is secure. Beanstalk assumes the security of Pipeline.
    
* Update the disclosure about Beanstalk requiring trustless and reliable access to a manipulation resistant oracle for a dollar:

    **BEANSTALK REQUIRES TRUSTLESS AND RELIABLE ACCESS TO A MANIPULATION RESISTANT PRICE ORACLE FOR A DOLLAR. BEANSTALK USES A CHAINLINK DATA FEED AND THE ON-CHAIN PRICES OF OTHER STABLECOINS TO DETERMINE THE PRICE OF A DOLLAR. THERE IS RISK ASSOCIATED WITH BOTH OF THESE METHODS THAT CAN COMPROMISE THEIR INTEGRITY AS ACCURATE PRICE ORACLES.**
    
    Beanstalk's core objective is to oscillate the price of a Bean above and below its dollar peg. To do this, Beanstalk must be able to reliably measure the price of a dollar on-chain without trusting a centralized third-party to provide it. A robust, decentralized stablecoin requires a tamper-proof, manipulation resistant and decentralized price oracle.
    
    Beanstalk measures the price of a dollar in each pool on the Oracle Whitelist:
    * For the BEAN:3CRV Curve pool, Beanstalk assumes the average value of each USDC, USDT and DAI in the pool is equal to $1 (3CRV consists of USDC, USDT and DAI).
    * For the BEAN:ETH Well, Beanstalk assumes the average value between the current price returned by the ETH/USD Chainlink data feed and either the ETH:USDC Uniswap V3 pool or the ETH:USDT Uniswap V3 pool is equal to $1.

    A disruption in the reliability of USDC, USDT, DAI or the ETH/USD Chainlink data feed could impact Bean minting, resulting in adverse consequences for Beanstalk. The Chainlink data feed is inherently centralized.
    
* Update the disclosure about Beanstalk not being a finished protocol and requiring development:

    **BEANSTALK IS NOT A FINISHED PROTOCOL AND REQUIRES ONGOING DEVELOPMENT. THERE IS NO GUARANTEE OF FURTHER DEVELOPMENT.**

    Beanstalk is likely not at a point where it can sustain itself in perpetuity without additional development of itself and the surrounding ecosystem. High quality contributions are required but are not guaranteed.

    Publius, the pseudonym for the three co-founders of Beanstalk, continues to be influential within the Beanstalk DAO. The identities of Publius are public. The identities of most of the remaining Beanstalk contributors are anonymous.

The updated Beanstalk DAO Disclosures can be read [here](https://arweave.net/zaOThrkaNCQ2zkkas8pyVQkFSkAKXfU7JahilBaqXRs).

### BFM Signers and BIC Members

We propose the following signers for the BFM:
- Michael Montoya
- Brean **(new signer)**
- guy
- sweetredbeans
- mod323
- aloceros
- Cujo

We propose the following members for the BIC:
- Brendan Sanderson
- mod323
- pizzaman1337 **(new member)**
- Brean
- funderberker
- malteasy

## Technical Rationale

### Basin Integration

Integrating Basin and its components into Beanstalk in a generalized fashion makes it easier for future developers to propose BIPs that whitelist Wells.

### Silo V3 Getters

Providing a way to get an individual Farmer's BDV per whitelisted token increases the composability of Beanstalk and enables protocols like Root to integrate with Silo V3.

### Pipeline Upgrade 

Supporting a version of Pipeline that can receive transfers of Ether improves the composability supported by the `farm` function.

### `gm` Incentive Reward Oracle

Querying a single liquidity pool to calculate the price of Ether in Beans reduces the cost of calling the `gm` function, and thus the reward that Beanstalk pays to incentive it.

### Flood Update

The Flood implementation must be updated in order to function properly.

## Economic Rationale

### Whitelist BEANETH

Beans currently only trade against 3CRV which is a significant centralization vector for Beanstalk. Ether is the most decentralized, censorship resistant and liquid asset on the Ethereum network. Thus, a relatively high Seeds per BDV for LP tokens of Beans paired with Ether is justified.

The Multi Flow Pump is the first Ethereum-native oracle for Ethereum-native data that offers multi-block MEV manipulation resistance in a post-Merge environment. Using Multi Flow, Beanstalk can calculate a multi-block MEV manipulation resistant BEAN:ETH price. 

Uniswap V3 pools are not multi-block MEV manipulation resistant but are on-chain and permissionless. Chainlink data feeds are multi-block MEV manipulation resistant because they are off-chain and permissioned. The ETH/USD data feed is the only Chainlink feed secured by staking. Using an average of the Chainlink feed and one of the Uniswap V3 pools, Beanstalk can calculate a multi-block MEV manipulation resistant ETH:USD price at the cost of some centralization.

Therefore, Beanstalk calculates deltaB in the BEANETH Well in a manipulation resistant fashion.

### Seeds per BDV Changes

**Rewarding more Seeds to LP tokens than Beans**

Providing liquidity for Beans is more useful to Beanstalk than Depositing Beans in the Silo, so Beanstalk should continue rewarding more Seeds to LP token Deposits than Bean Deposits. The delta in Seeds per BDV between LP tokens and Beans also minimizes upside volatility (and therefore excessive minting) as Farmers are incentivized to Convert their Deposited Beans to Deposited LP tokens above peg.

Therefore, LP tokens continue to receive more Seeds per BDV upon Deposit than Beans.

**Rewarding more Seeds to BEANETH than BEAN3CRV Deposits**

Prior to the April 2022 exploit, BEAN3CRV and BEANETH Deposits were both rewarded 4 Seeds per BDV. During this time, BEAN3CRV liquidity was growing rapidly and surpassed the amount of BEANETH liquidity. This is not ideal as Beanstalk should incentivize providing liquidity for Beans against decentralized assets.

Therefore, BEANETH should receive more Seeds per BDV than BEAN3CRV.

**Increasing Seeds for Bean and BEANETH Deposits**

Unripe Depositors had accumulated Grown Stalk for about a year from Replant to the commitment of Silo V3. As a result, the average Grown Stalk per BDV is excessively high. Increasing the Seeds per BDV for Bean and BEANETH allows for new Depositors to catch up to the average Grown Stalk per BDV faster. 

With 4.5 Seeds per BDV for BEANETH Deposits, a new BEANETH Depositor would catch up to the average Grown Stalk per BDV in about 6 months. With 3 Seeds per BDV for Bean Deposits, a new Bean Depositor would catch up to the average Grown Stalk per BDV in about 9 months. A function to evaluate Seeds per BDV and time to catch up to average can be evaluated here: https://www.desmos.com/calculator/hevziu8k9y.

Additionally, decreasing the ratio of LP token to Bean Seeds per BDV reduces the incentive to not Convert from LP tokens to Beans below peg.

Therefore, the Seeds per BDV for Bean, BEAN3CRV and BEANETH are set to 3 Seeds, 3.25 Seeds and 4.5 Seeds, respectively.

### `gm` Incentive Reward Oracle

Changing the oracle for calculating the cost to execute the `gm` function in Beans to the BEANETH Well that uses the Multi Flow Pump improves the censorship resistance of Beanstalk due to its multi-block MEV manipulation resistance.

### Flood Update

Flood is an essential peg maintenance tool for Beanstalk.

## Contract Changes

### Initialization Contract

The `init` function on the following `InitBipBasinIntegration` contract is called:

- [`0xdCCcCeEEaD066C20075eE0EaF548685A3215204D`](https://etherscan.io/address/0xdCCcCeEEaD066C20075eE0EaF548685A3215204D#code)

### Silo Facet

The following `SiloFacet` is removed from Beanstalk:

- [`0x7D98D7b3486b228b1B449AB7360b72869C2DEF4F`](https://etherscan.io/address/0x7D98D7b3486b228b1B449AB7360b72869C2DEF4F#code)

The following `SiloFacet` is added to Beanstalk:

- [`0xf4B3629D1aa74eF8ab53Cc22728896B960F3a74E`](https://etherscan.io/address/0xf4B3629D1aa74eF8ab53Cc22728896B960F3a74E#code)

#### `SiloFacet` Function Changes

| Name                    | Selector     | Action  | Type | New Functionality |
|:------------------------|:-------------|:--------|:-----|:------------------|
| `balanceOf`             | `0x00fdd58e` | Replace | View |                   |
| `balanceOfBatch`        | `0x4e1273f4` | Replace | View |                   |
| `balanceOfDepositedBDV` | `0xbc8514cf` | Add     | View | ✓                 |
| `balanceOfEarnedBeans`  | `0x3e465a2e` | Replace | View |                   |
| `balanceOfEarnedStalk`  | `0x341b94d5` | Replace | View |                   |
| `balanceOfGrownStalk`   | `0x8915ba24` | Replace | View |                   |
| `balanceOfPlenty`       | `0x896651e8` | Replace | View |                   |
| `balanceOfRainRoots`    | `0x69fbad94` | Replace | View |                   |
| `balanceOfRoots`        | `0xba39dc02` | Replace | View |                   |
| `balanceOfSop`          | `0xa7bf680f` | Replace | View |                   |
| `balanceOfStalk`        | `0x8eeae310` | Replace | View |                   |
| `bdv`                   | `0x8c1e6f22` | Replace | View |                   |
| `getDeposit`            | `0x61449212` | Replace | View |                   |
| `getDepositId`          | `0x98f2b8ad` | Replace | View |                   |
| `getLastMowedStem`      | `0x7fc06e12` | Add     | View | ✓                 |
| `getMowStatus`          | `0xdc25a650` | Add     | View | ✓                 |
| `getSeedsPerToken`      | `0x9f9962e4` | Replace | View |                   |
| `getTotalDeposited`     | `0x0c9c31bd` | Replace | View |                   |
| `getTotalDepositedBDV`  | `0x9d6a924e` | Replace | View |                   |
| `grownStalkForDeposit`  | `0x3a1b0606` | Replace | View |                   |
| `inVestingPeriod`       | `0x0b2939d1` | Replace | View |                   |
| `lastSeasonOfPlenty`    | `0xbe6547d2` | Replace | View |                   |
| `lastUpdate`            | `0xcb03fb1e` | Replace | View |                   |
| `migrationNeeded`       | `0xc38b3c18` | Replace | View |                   |
| `seasonToStem`          | `0x896ab1c6` | Replace | View |                   |
| `stemStartSeason`       | `0xbc771977` | Replace | View |                   |
| `stemTipForToken`       | `0xabed2d41` | Replace | View |                   |
| `tokenSettings`         | `0xe923e8d4` | Replace | View |                   |
| `totalEarnedBeans`      | `0xfd9de166` | Replace | View |                   |
| `totalRoots`            | `0x46544166` | Replace | View |                   |
| `totalStalk`            | `0x7b52fadf` | Replace | View |                   |
| `claimPlenty`           | `0x45947ba9` | Replace | Call |                   |
| `deposit`               | `0xf19ed6be` | Replace | Call |                   |
| `mow`                   | `0x150d5173` | Replace | Call |                   |
| `mowMultiple`           | `0x7d44f5bb` | Replace | Call |                   |
| `plant`                 | `0x779b3c5c` | Replace | Call |                   |
| `safeBatchTransferFrom` | `0x2eb2c2d6` | Replace | Call |                   |
| `safeTransferFrom`      | `0xf242432a` | Replace | Call |                   |
| `transferDeposit`       | `0x081d77ba` | Replace | Call |                   |
| `transferDeposits`      | `0xc56411f6` | Replace | Call |                   |
| `withdrawDeposit`       | `0xe348f82b` | Replace | Call |                   |
| `withdrawDeposits`      | `0x27e047f1` | Replace | Call |                   |

#### `SiloFacet` Event Changes

None.

### Season Facet

The following `SeasonFacet` is removed from Beanstalk:

- [`0x3981E1b15c6CBb48953522A0F0aaCfE14074FFd5`](https://etherscan.io/address/0x3981E1b15c6CBb48953522A0F0aaCfE14074FFd5#code)

The following `SeasonFacet` is added to Beanstalk:

- [`0x17b31771A04af17B131246C3C9d442e3c3908A27`](https://etherscan.io/address/0x17b31771A04af17B131246C3C9d442e3c3908A27#code)

#### `SeasonFacet` Function Changes

| Name                 | Selector     | Action  | Type | New Functionality |
|:---------------------|:-------------|:--------|:-----|:------------------|
| `abovePeg`           | `0x2a27c499` | Replace | View |                   |
| `curveOracle`        | `0x07a3b202` | Add     | View | ✓                 |
| `paused`             | `0x5c975abb` | Replace | View |                   |
| `plentyPerRoot`      | `0xe60d7a83` | Replace | View |                   |
| `poolDeltaB`         | `0x471bcdbe` | Replace | View | ✓                 |
| `rain`               | `0x43def26e` | Replace | View |                   |
| `season`             | `0xc50b0fb0` | Replace | View |                   |
| `seasonTime`         | `0xca7b7d7b` | Replace | View |                   |
| `sunriseBlock`       | `0x3b2ecb70` | Replace | View |                   |
| `time`               | `0x16ada547` | Replace | View |                   |
| `totalDeltaB`        | `0x06c499d8` | Replace | View | ✓                 |
| `weather`            | `0x686b6159` | Replace | View |                   |
| `wellOracleSnapshot` | `0x597490c0` | Add     | View | ✓                 |
| `gm`                 | `0x64ee4b80` | Replace | Call | ✓                 |
| `sunrise`            | `0xfc06d2a6` | Replace | Call |                   |

#### `SeasonFacet` Event Changes

None.

### Convert Facet

The following `ConvertFacet` is removed from Beanstalk:

- [`0x6334Da4a08b22E612b6A00321601Fd2f2E6a821C`](https://etherscan.io/address/0x6334Da4a08b22E612b6A00321601Fd2f2E6a821C#code)

The following `ConvertFacet` is added to Beanstalk:

- [`0xC2f8F1412d10E4DC79D34a46ab1d3d862517f939`](https://etherscan.io/address/0xC2f8F1412d10E4DC79D34a46ab1d3d862517f939#code)

#### `ConvertFacet` Function Changes

| Name                  | Selector     | Action   | Type | New Functionality |
|:----------------------|:-------------|:---------|:-----|:------------------|
| `convert`             | `0xb362a6e8` | Replace  | Call |                   |

#### `ConvertFacet` Event Changes

| Name              | Change                                            |
|:------------------|:--------------------------------------------------|
| `RemoveDeposit`   | Removed                                           |

### Convert Getters Facet

The following `ConvertGettersFacet` is added to Beanstalk:

- [`0x912f505ecD6536733da22BB4349595aA36806918`](https://etherscan.io/address/0x912f505ecD6536733da22BB4349595aA36806918#code)

#### `ConvertGettersFacet` Function Changes

| Name                  | Selector     | Action   | Type | New Functionality |
|:----------------------|:-------------|:---------|:-----|:------------------|
| `getAmountOut`        | `0x4aa06652` | Replace  | View |                   |
| `getMaxAmountIn`      | `0x24dd285c` | Replace  | View |                   |

#### `ConvertGettersFacet` Event Changes

None.

### Whitelist Facet

The following `WhitelistFacet` is removed from Beanstalk:

- [`0xF286Bb8297DdB248Fbde33BD1E309778DA930795`](https://etherscan.io/address/0xF286Bb8297DdB248Fbde33BD1E309778DA930795#code)

The following `WhitelistFacet` is added to Beanstalk:

- [`0x730bfC44C8c51c469aFc133B0e445d0CC9FFc63d`](https://etherscan.io/address/0x730bfC44C8c51c469aFc133B0e445d0CC9FFc63d#code)

#### `WhitelistFacet` Function Changes

| Name                                 | Selector     | Action   | Type | New Functionality |
|:-------------------------------------|:-------------|:---------|:-----|:------------------|
| `dewhitelistToken`                   | `0x86b40a1b` | Replace  | Call |                   |
| `updateStalkPerBdvPerSeasonForToken` | `0xf18d9ed0` | Replace  | Call |                   |
| `whitelistToken`                     | `0xd8a6aafe` | Replace  | Call | ✓                 |
| `whitelistTokenWithEncodeType`       | `0xb4f55be8` | Add      | Call | ✓                 |

#### `WhitelistFacet` Event Changes

| Name                           | Change                                           |
|:-------------------------------|:-------------------------------------------------|
| `WhitelistToken`               | `stalk` variable renamed to `stalkIssuedPerBDV`  |

### Metadata Facet

The following `MetadataFacet` is being removed from Beanstalk:

- [`0xd16B381CC6d5991F012C238f02F50aF3bd9f6A20`](https://etherscan.io/address/0xd16B381CC6d5991F012C238f02F50aF3bd9f6A20#code)

The following `MetadataFacet` is added to Beanstalk:

- [`0x5e6991aFa1352822e769c873200954B4dE6c6E48`](https://etherscan.io/address/0x5e6991aFa1352822e769c873200954B4dE6c6E48#code)

#### `MetadataFacet` Function Changes

| Name                   | Selector     | Action  | Type | New Functionality |
|:-----------------------|:-------------|:--------|:-----|:------------------|
| `imageURI`             | `0x8f742d16` | Remove  | View |                   |
| `imageURI`             | `0xc20b8071` | Add     | View | ✓                 |
| `name`                 | `0x06fdde03` | Add     | View | ✓                 |
| `symbol`               | `0x95d89b41` | Add     | View | ✓                 |
| `uri               `   | `0x0e89341c` | Replace | View | ✓                 |

#### `MetadataFacet` Event Changes

| Name              | Change                                            |
|:------------------|:--------------------------------------------------|
| `URI`             | Added                                             |

### Depot Facet

The following `DepotFacet` is being removed from Beanstalk:

- [`0xd812fdfb45bc4d05884eb270f7ddfaac71d60f78`](https://etherscan.io/address/0xd812fdfb45bc4d05884eb270f7ddfaac71d60f78#code)

The following `DepotFacet` is added to Beanstalk:

- [`0xeEe1D0238025BFcdE4e8516ceC5DB524ca4d5A55`](https://etherscan.io/address/0xeEe1D0238025BFcdE4e8516ceC5DB524ca4d5A55#code)

#### `DepotFacet` Function Changes

| Name                   | Selector     | Action  | Type | New Functionality |
|:-----------------------|:-------------|:--------|:-----|:------------------|
| `readPipe`             | `0xdd756c4f` | Replace | View |                   |
| `advancedPipe`         | `0xb452c7ae` | Replace | Call |                   |
| `etherPipe`            | `0x6e47d07b` | Replace | Call |                   |
| `multiPipe`            | `0xcabec62b` | Replace | Call |                   |
| `pipe`                 | `0x08e1a0ab` | Replace | Call |                   |

#### `DepotFacet` Event Changes

None.

### BDV Facet

The following `BDVFacet` is being removed from Beanstalk:

- [`0xc17ED2e41242063DB6b939f5601bA01374b9D44a`](https://etherscan.io/address/0xc17ED2e41242063DB6b939f5601bA01374b9D44a#code)

The following `BDVFacet` is added to Beanstalk:

- [`0x9Cb54A8eAcD4d295dd02833cd2bdD385173c7fF5`](https://etherscan.io/address/0x9Cb54A8eAcD4d295dd02833cd2bdD385173c7fF5#code)

#### `BDVFacet` Function Changes

| Name                   | Selector     | Action  | Type | New Functionality |
|:-----------------------|:-------------|:--------|:-----|:------------------|
| `beanToBDV`            | `0x5a049a47` | Replace | View |                   |
| `curveToBDV`           | `0xf984019b` | Replace | View |                   |
| `unripeBeanToBDV`      | `0xc8cda2a0` | Replace | View |                   |
| `unripeLPToBDV`        | `0xb0c22bb1` | Replace | View |                   |
| `wellBdv`              | `0xc84c7727` | Add     | View | ✓                 |

#### `BDVFacet` Event Changes

None.

### Enroot Facet

The following `EnrootFacet` is added to Beanstalk:

- [`0x1C2a836184d2fa7e4d0750Af73423a076cd169CE`](https://etherscan.io/address/0x1C2a836184d2fa7e4d0750Af73423a076cd169CE#code)

#### `EnrootFacet` Function Changes

| Name                   | Selector     | Action  | Type | New Functionality |
|:-----------------------|:-------------|:--------|:-----|:------------------|
| `enrootDeposit`        | `0x0b58f073` | Replace | Call |                   |
| `enrootDeposits`       | `0x88fcd169` | Replace | Call |                   |

#### `EnrootFacet` Event Changes

| Name              | Change                                            |
|:------------------|:--------------------------------------------------|
| `RemoveDeposit`   | Moved from `ConvertFacet`                         |
| `RemoveDeposits`  | Copied from `ConvertFacet`                        |

## Beans Minted 

None.

## Audit

### Basin Integration

The commit hash of this BIP is [76a188233917afc1db103ca64b02dab8c5280b35](https://github.com/BeanstalkFarms/Beanstalk/tree/76a188233917afc1db103ca64b02dab8c5280b35).

Halborn has performed an audit of this BIP up to commit hash [78d7045a4e6900dfbdc5f1119b202b4f30ff6ab8](https://github.com/BeanstalkFarms/Beanstalk/tree/78d7045a4e6900dfbdc5f1119b202b4f30ff6ab8). You can view the final audit report [here](https://arweave.net/qD2rZqlJgjBj-VatwoDmNgwdXd8Omc1Bt9RTkPsvK8k). 

Beanstalk contract changes between the two commits:
- Updated the Pipeline address in `DepotFacet`;
- Added metadata for BEANETH to the `MetadataFacet`;
- Changed the `stem` type in the `RemoveDeposit` event signature in `EnrootFacet` from `int128` to `int96`;
- Added the `InitBipBasinIntegration` contract that calls `whitelistToken` on BEANETH and sets the new Seeds per BDV for Bean, BEAN3CRV and BEANETH;
- Split up `ConvertFacet` into `ConvertFacet` and `ConvertGettersFacet`;
- Updated the Flood implementation (see `handleRain`) to reflect the removal of the Withdrawal Freeze;
- Added the `name` and `symbol` functions to `MetadataFacet`; 
- Updated Uniswap V3 pool calls in the ETH:USD oracle to handle failure gracefully; and
- Updated the ETH:USD oracle parameters.

### Basin

Halborn and Cyfrin performed an audit of Basin up to commit hash [e5441fc78f0fd4b77a898812d0fd22cb43a0af55](https://github.com/BeanstalkFarms/Basin/tree/e5441fc78f0fd4b77a898812d0fd22cb43a0af55). You can view the final audit report from Halborn [here](https://arweave.net/1IKi4iqRvu3qu_GdcLY4f3u9PgvOMvuRltU7AIXAEyA) and the final audit report from Cyfrin [here](https://arweave.net/usT3ClfjHwpX32OXnh5De1aH79csX1PMoXJCghXBaps). 

[Code4rena](https://code4rena.com/contests/2023-07-basin) performed an audit of Basin up to commit hash [8f6709ee1eeb36a452a08c591ebd3a340ea9b8c4](https://github.com/BeanstalkFarms/Basin/tree/8f6709ee1eeb36a452a08c591ebd3a340ea9b8c4), which included all remediations from the previous two audits. 

The formal audit report from Code4rena is forthcoming, but all findings were either acknowledged or remediated by commit hash [ea51be32047cbb6625232cb63b180ecfb3fd0b71](https://github.com/BeanstalkFarms/Basin/tree/ea51be32047cbb6625232cb63b180ecfb3fd0b71), which is the commit hash of Basin code deployed on Ethereum mainnet. The changelog can be read [here](https://github.com/BeanstalkFarms/Basin/pull/122). All updates were reviewed by Cyfrin.

## Effective

Immediately upon commitment.
