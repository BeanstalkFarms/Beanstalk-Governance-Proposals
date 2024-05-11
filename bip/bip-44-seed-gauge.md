# BIP-44: Seed Gauge System

Proposed: May 1, 2024

Status: Failed

Link: [Snapshot](https://snapshot.org/#/beanstalkdao.eth/proposal/0xdf262b24e2dffbe1eff04815bb1ca3e73da9636b7d48e0dcd9bfb26daf2e271d), [Arweave](https://arweave.net/zYTWn7E74vRLSeRrqGrE7TbPVEYqQAiCpIF2uvvzyA0)

---

- [Proposer](#proposer)
- [Summary](#summary)
- [Links](#links)
- [Context](#context)
- [Problem](#problem)
- [Proposed Solution](#proposed-solution)
- [Technical Rationale](#technical-rationale)
- [Economic Rationale](#economic-rationale)
- [Contract Changes](#contract-changes)
- [Beans Minted](#beans-minted)
- [Audit](#audit)
- [Effective](#effective)

## Proposer

Beanstalk Farms, Ben Weintraub, Brendan Sanderson

Proposer Wallet: [0x9e0cb69ae6a5ad4eb870eb18d051efe642ed7db4](https://etherscan.io/verifySig/41639)

## Summary

* Implement the Seed Gauge System;
    * Implement the ability to set a target number of Seasons for a new Deposit with an average number of Seeds to catch up to the average Grown Stalk per BDV of existing Deposits at the time of Deposit (*i.e.*, a target Grown Stalk inflation rate);
    * Introduce the ability to set an optimal distribution of Deposited LP BDV amongst whitelisted LP tokens; and
    * Introduce the ability to change the distribution of Grown Stalk to various whitelisted assets each Season in attempt to return to ideal equilibrium:
        * Change the distribution of Grown Stalk between Deposited Beans and LP tokens based on the Bean to Max LP Ratio (*i.e.*, the ratio of Grown Stalk issuance between 1 Bean and 1 BDV of the LP token with the highest Gauge Points (defined below) per BDV); and
        * Change the distribution of Grown Stalk between various whitelisted LP tokens based on their current and optimal distributions;
* Initialize the Seed Gauge System;
    * Set the target number of Seasons for a new Deposit with an average number of Seeds to catch up to the average Grown Stalk per BDV to 4320 Seasons (~6 months);
    * Set the minimum Bean to Max LP Ratio to 1:2 (*i.e.*, at most, 1 BDV of an LP token Deposit will receive twice as much Grown Stalk per Season as a 1 Bean Deposit);
    * Set the maximum Bean to Max LP Ratio to 1:1 (*i.e.*, at most, a 1 Bean Deposit will receive as much Grown Stalk per Season as 1 BDV of an LP token Deposit with the highest Grown Stalk per BDV.);
    * Initialize the Bean to Max LP Ratio to 1:1.5 (*i.e.*, a 1.5 Bean Deposit will receive as much Grown Stalk per Season as 1 BDV of an LP token Deposit with the highest Grown Stalk per BDV.);
    * Set the optimal target distribution of Deposited LP BDV to 100% BEANETH;
    * Initialize BEANETH's Gauge Points (which determine Grown Stalk issuance across various LP tokens) to 100; and
    * Implement a default Gauge Point function that increments or decrements the Gauge Points of a whitelisted LP token based on whether the current Deposited BDV is lower or higher than optimal;
* Upgrade the case system (how Beanstalk evaluates its position and current state with respect to ideal equilibrium) to Cases V2:
    * Add a fourth axis on which Beanstalk evaluates itself, the Liquidity to Supply Ratio (L2SR) in addition to (1) price, (2) Pod Rate and (3) change in demand for Soil;
        * Include support for only factoring in a portion of liquidity in a whitelisted pool into the L2SR calculation (*i.e.*, liquidity weight);
        * Set the liquidity weight for BEANETH to 100% (*i.e.*, do not discount the liquidity);
    * Implement an excessively high Bean price case at P > 1.05;
    * Add support for changing the Maximum Temperature in response to the cases in a relative manner;
    * Add support for changing the Bean to Max LP Scalar (which determines the Bean to Max LP Ratio) in response to the cases in a relative and absolute manner;
    * Change the absolute Maximum Temperature changes for the P < 1 and increasing/steady demand for Soil cases; and
    * Initialize the relative Maximum Temperature changes and both the relative and absolute Bean to Max LP Scalar changes by case;
* Implement the second step in a two step process to track Deposited BDV that has not migrated to Silo V3;
* Support Unripe λ → λ Conversions in the Silo;
* Remove BEAN3CRV from the Deposit and Minting Whitelists;
* Remove the BEAN → BEAN3CRV Conversion from the Convert Whitelist; 
* Update Flood to occur in the whitelisted Well with the most liquidity; 
* Implement Germination, which introduces a 2 Season delay in receiving Earned Beans after Deposit; and
* Increase the max `gm` reward during the first available block from 100 to 250 Beans.

Note: BIP-42 was cancelled due to a bug that was found during the Voting Period. A BCM Signer indicated that the transaction should be cancelled by signing a [verified message on Etherscan](https://etherscan.io/verifySig/41632). As such, BIP-42 was removed from Snapshot. The fix for the reported bug was reviewed by Cyfrin.

## Links

* [BIP-44 GitHub PR](https://github.com/BeanstalkFarms/Beanstalk/pull/722)
* GitHub Commit Hash: [ac8e681c7daa7cb046c1e405b27e50e7e44c0504](https://github.com/BeanstalkFarms/Beanstalk/pull/722/commits/ac8e681c7daa7cb046c1e405b27e50e7e44c0504)
* [Safe Transaction](https://app.safe.global/transactions/tx?id=multisig_0xa9bA2C40b263843C04d344727b954A545c81D043_0x844870e199785e54cd2ae50527eea6e216241385771119eb6a2aa0083f60bec5&safe=eth:0xa9bA2C40b263843C04d344727b954A545c81D043)

## Context

Beanstalk is a credit-based stablecoin protocol. The main goal of Beanstalk is to regularly oscillate the Bean price across $1, indefinitely. Beanstalk's primary peg maintenance mechanism, and theoretical basis for existence, is the Field (*i.e.*, the Beanstalk credit facility). The ability for Beanstalk to borrow Beans from the open market at a reasonable interest rate is essential for long term peg maintenance. 

However, with the successful addition of Conversions within the Silo in December 2021, a second peg maintenance mechanism was discovered. The ability to add and remove Beans from liquidity pools to decrease and increase the Bean price, respectively, changes the Bean price without any outflows or inflows from the system. Currently, Converts are limited in that Depositors can only Convert between Beans and LP tokens, and vice versa (*i.e.*, not across LP tokens). Furthermore, the incentives for Converting in a given direction with respect to peg (in particular, Seed allocations to the various assets whitelisted for Deposit) are not adjusted autonomously in response to Beanstalk's state.

At the beginning of each Season, Beanstalk adjusts its parameters as a response to its position and current state in pursuit of its main goal: to regularly oscillate the Bean price across $1, indefinitely. Therefore, the two primary questions with respect to peg maintenance are: 
1. How should Beanstalk classify its position and state at the beginning of each Season?
2. How should Beanstalk respond to its position and state at the beginning of each Season in an attempt to return to its ideal position and state?

For a thorough investigation into these questions, it is recommend that all engaged Farmers read the [Beanstalk State Space](https://bean.money/beanstalk-state-space.pdf) presentation and listen to the recordings of the discussion. However, for the purposes of this BIP, the punchline is that to date, Beanstalk has only measured price (in practice, deltaB) and debt level (in practice, the Pod Rate) in order to determine its health and its corresponding response. With the introduction of the Seed Gauge System, now Beanstalk also measures its liquidity level (in practice, the Liquidity to Supply Ratio, or L2SR).

This represents the potential for a significant efficiency increase in terms of peg maintenance, particularly in times of very high debt levels where Beanstalk prefers peg maintenance to primarily take place within the Silo via Conversions.

The general intuition for the Seed Gauge System is as follows: Seeds generate opportunity cost for Withdrawing assets that have been Deposited for longer AND marginal benefit for holding particular assets in the Silo in the form of Grown Stalk. 

There are 3 new primary tools that Beanstalk has at its disposal as a result of implementing the Seed Gauge System:
1. The Target Seasons to Catchup, which determines the target number of Seasons for a new Deposit with an average number of Seeds to catch up to the average Grown Stalk per BDV of existing Deposits at the time of Deposit;
2. Bean vs LP Seed distribution, or more specifically, the Bean to Max LP Ratio, which determines the relative benefits of holding Bean exposure vs exposure to at least 1 particular LP token in the Silo over time; and
3. LP vs LP Seed distribution, which determines relative benefits of holding a given non-Bean asset in the Silo over time.

Tools 1 and 3 are relevant to the Seed Gauge System but not relevant to the general peg maintenance mechanism and can be thought of as further refinements of the model. 

Tool 2 is central to the Seed Gauge System and the general peg maintenance mechanism. Up until [BIP-37](https://arweave.net/VPF1EafHb41Y7AjzUYB0j-mR8Sl0KxPknDJYffczBpc), the effective Bean to Max LP Ratio was fixed at 1:2 (Deposited Beans and BEAN3CRV earned 2 and 4 Seeds per BDV, respectively). Since BIP-37, the ratio has been fixed at 1:1.5, given that Depositing Beans and BEANETH earned 3 and 4.5 Seeds per BDV, respectively. With the Seed Gauge System, this ratio can now be adjusted autonomously.

## Problem

Beanstalk does not currently have a way to target a particular Grown Stalk inflation rate. This means that there is no way for Beanstalk to affect the time in which it takes new Silo Deposits to catch up in terms of Grown Stalk per BDV to older Deposits. As a result of long periods without Bean mints, it is possible for older Deposits to have such a Grown Stalk advantage that it discourages newer Deposits.

Seeds per BDV for whitelisted assets are currently static and can only be changed via governance. Considering the outsized importance of Conversions within the Silo, this is a missing tool in the Beanstalk toolbox: by changing the Seeds per BDV for a pair of assets, Beanstalk can change the desirability of holding one asset over the other. In theory, dynamic Seed issuance can significantly improve peg maintenance. 

The L2SR is an important indicator of Beanstalk's health, but Beanstalk currently does not measure or use it to evaluate its position and current state with respect to ideal equilibrium. Doing so requires an upgrade to the case system.

The current case system only has two different cases for price, P < 1 and P > 1. A very high P compromises utility of Bean because the potential for price spikes makes Beans less attractive to borrow and price other assets against.

Beanstalk currently changes Maximum Temperature on an absolute scale, but does not support the ability to change it on a relative scale. In instances where the Maximum Temperature is particularly high, the lack of ability to change the Maximum Temperature on a relative basis may lead to inefficiencies in the Field.

When P < 1 and demand for Soil is increasing or steady, Beanstalk is currently more aggressive in increasing the Maximum Temperature than it should be, in theory, when the debt level is high, and less aggressive than it should be, in theory, when the debt level is low.

BDV that has not been migrated to Silo V3 is not included in `s.siloBalances[token].depositedBdv`. For Beanstalk to support a LP Gauge System, it is necessary to track the total Deposited BDV for each whitelisted token.

Farmers can only Chop Unripe assets by forfeiting all their associated Grown Stalk, even if they intend to leave the underlying assets in the Silo after the Chop is performed.

BEAN3CRV:
* Is exposed to the centralization risks of USDC, USDT and DAI;
* Is priced as the least of USDC, USDT and DAI;
* Uses an oracle that is not resistant to inter-block MEV manipulation; and
* Is subject to unnecessary trading fees that negatively affect peg maintenance;

Flood is only implemented in the BEAN3CRV pool, which has low liquidity and is dewhitelisted in this upgrade.

Implementing the Seed Gauge System without any changes to Earned Bean issuance would open up potential vectors for manipulation of Deposited BDV, and thus Seed allocations for various whitelisted assets.

The `gm` function is not regularly being called in the first block in which it becomes callable due to the increased gas costs on Ethereum and the low max reward of 100 Beans. 

## Proposed Solution

### Seed Gauge System

#### Grown Stalk Inflation Rate

The Grown Stalk per BDV for a Deposit can be thought of as the age of that Deposit—the longer the Deposit has been in the Silo, the higher its Grown Stalk is relative to its BDV. Thus, the average Grown Stalk per BDV is a measure for how old Deposits are across the entire Silo relative to the total BDV in the Silo.

The Seed Gauge System enables Beanstalk to target an amount of Grown Stalk per BDV that should be issued each Season. In particular, Beanstalk sets the `TARGET_SEASONS_TO_CATCHUP` parameter, which determines how many Seasons it should take for a new Depositor to catch up to the average Grown Stalk per BDV. The average Grown Stalk per BDV per Season is the average Grown Stalk per BDV divided by `TARGET_SEASONS_TO_CATCHUP`. We propose that `TARGET_SEASONS_TO_CATCHUP` be set to `4320`, or ~6 months.

#### Gauge Points

Gauge Points determine how the Grown Stalk issued in a Season should be distributed between whitelisted LP tokens. Only whitelisted LP tokens have Gauge Points. Gauge Points have 18 decimal precision (`1e18`).

Every Season, Beanstalk calculates the new amount of Gauge Points an LP token should have by calling each whitelisted LP token's Gauge Point function. 

We propose that the Gauge Points for BEANETH (the only whitelisted LP token as of this upgrade) be initialized to 100, and that BEANETH utilize the following Gauge Point function to update its Gauge Points each Season:

``` 
function defaultGaugePointFunction(
    uint256 currentGaugePoints,
    uint256 optimalPercentDepositedBdv,
    uint256 percentOfDepositedBdv
) external pure returns (uint256 newGaugePoints) {
    if (
        percentOfDepositedBdv > optimalPercentDepositedBdv.mul(UPPER_THRESHOLD).div(THRESHOLD_PRECISION)
    ) {
        // gauge points cannot go below 0.
        if (currentGaugePoints <= ONE_POINT) return 0;
        newGaugePoints = currentGaugePoints.sub(ONE_POINT);
    } else if (
        percentOfDepositedBdv <
        optimalPercentDepositedBdv.mul(LOWER_THRESHOLD).div(THRESHOLD_PRECISION)
    ) {
        newGaugePoints = currentGaugePoints.add(ONE_POINT);

        // Cap gaugePoints to MAX_GAUGE_POINTS if it exceeds.
        if (newGaugePoints > MAX_GAUGE_POINTS) return MAX_GAUGE_POINTS;
    } else {
        // If % of deposited BDV is .01% within range of optimal, 
        // keep gauge points the same.
        return currentGaugePoints;
    }
}
```

Thus, given that BEANETH will be the only whitelisted LP token, the BEANETH Gauge Points will remain at 100 until another LP token is whitelisted.

This function takes as input `optimalPercentDepositedBdv`, (*i.e.*, the optimal distribution of Deposited BDV amongst whitelisted LP tokens). Put another way, this is the target distribution of liquidity for Beans to trade against. For the time being, the optimal distribution of Deposited BDV will be set and upgraded via normal governance. Given the single whitelisted LP token, we propose the optimal distribution of Deposited BDV amongst whitelisted LP tokens be set to 100% BEANETH.

#### Bean to Max LP Ratio

Gauge Points Per BDV is the amount of Gauge Points allocated to a whitelisted LP token divided by the total BDV of that LP token Deposited in the Silo. The whitelisted LP token with the largest Gauge Points per BDV is used to determine the distribution of Grown Stalk to Beans in the Silo in order to ensure that at least 1 LP token always receives at least as much Grown Stalk as Deposited Beans (*i.e.*, the "Max LP" in "Bean to Max LP Ratio").

The Bean to Max LP Scalar ($L$) is a scalar between 0 and 1 that is adjusted by Cases V2 (defined later). The Bean to Max LP Ratio has a minimum ($Y$) and maximum value ($Z$). We propose to set $Y$ and $Z$ to 1:2 (*i.e.*, 50%, 0.5) and 1:1 (*i.e.*, 100%, 1), respectively.

$$ Bean To Max LP Ratio = Y + L(Z - Y) $$

At $L = 0$, the Bean to Max LP Ratio is at its minimum value (*i.e.*, at 1:2, 1 Bean Deposited in the Silo receives half the amount of Grown Stalk as 1 BDV of the Max LP token), and at $L = 1$, the Bean to Max LP Ratio is at its maximum value (*i.e.*, at 1:1, 1 Bean Deposited in the Silo receives the same amount of Grown Stalk as 1 BDV of the the Max LP token).

#### Chain of Logic 

Below is the series of steps that Beanstalk uses to determine how much Grown Stalk to issue each Season, and how to distribute that Grown Stalk to each whitelisted asset (Unripe assets excluded). Note that variables names listed below do not necessarily correspond to the Solidity implementation.

* `G` = average Grown Stalk per BDV per Season
* `totalGaugeBdv` = total BDV of all Deposited LP tokens and Deposited Beans
* `newGrownStalk` = `G` * `totalGaugeBdv` (the Grown Stalk issued this Season)
* `gaugePointsPerBdv[lpToken]` = Gauge Points per BDV for a particular token
* `beanToMaxLpRatio` = $Y + L(Z - Y)$ 
* `beanGaugePointsPerBdv` = `max(gaugePointsPerBdv[lpToken])` * `beanToMaxLpRatio`
* `totalGaugePoints` = `sum(lpGaugePoints[lpToken])` + `beanGaugePointsPerBdv`
* `grownStalkPerGaugePoint` = `newGrownStalk` / `totalGaugePoints`
* `grownStalkIssuedPerSeason[lpToken]` = `grownStalkPerGaugePoint` * `gaugePointsPerBdv[lpToken]`
* `grownStalkIssuedPerSeason[BEAN]` = `grownStalkPerGaugePoint` * `beanGaugePointsPerBdv`

### Cases V2

The case system is how Beanstalk evaluates its position and current state with respect to ideal equilibrium. Cases V1 measures 3 axes:
1. Price (2 cases: P > 1 and P < 1)
2. Pod Rate (4 cases: excessively low, reasonably low, reasonably high and excessively high)
3. Changing demand for Soil (3 cases: decreasing, steady and increasing)

As a result, there are 24 cases that determine how to adjust the Maximum Temperature:

![image](https://arweave.net/C3AdGUh3PS_y_X_t_z8WT4wE0g-WyHhW_XBdSuNSifs)

We propose Cases V2, which adds a fourth axis in the form of the L2SR and adds an additional case to price (excessively high price; P > Q). 

With 4 cases for L2SR (excessively low, reasonably low, reasonably high and excessively high) and 3 cases for price (low, high and excessively high), Cases V2 has 144 cases. Each case determines how to change the Maximum Temperature and the Bean to Max LP Scalar.

In Cases V2, support for Beanstalk to adjust its parameters (Maximum Temperature and the Bean to Max LP Scalar) on a relative basis is added in addition to the ability to change the values on an absolute basis.

#### Liquidity to Supply Ratio

In the context of the L2SR, liquidity is defined as the sum of the USD values of the non-Bean assets in each whitelisted liquidity pool multiplied by their respective liquidity weights. Supply is defined as the total Bean supply minus Locked Beans (defined below).

Beanstalk can be in 4 different states in relation to its L2SR:
 - Excessively high L2SR: above 80%;
 - Reasonably high L2SR: between 40% and 80%;
 - Reasonably low L2SR: between 12% and 40%; or
 - Excessively low L2SR: below 12%.

#### Locked Beans

Due to the Barn Raise and the associated Beans underlying Unripe assets, the number of tradable Beans does not equal the total Bean supply. At the time of writing, ~0.224 Beans underlying each Unripe Bean, but holders can only redeem ~0.0102 Beans per Unripe Bean. Thus, ~0.2138 Beans Per Unripe are considered not tradable. This is calculated and omitted from the Bean supply in `LibEvaluate.calcLPToSupplyRatio` via `LibUnripe.getLockedBeans`.

#### P > Q

Currently, Beanstalk only has 2 cases for price: below and above peg. During times where the price is excessively high, Beanstalk should have the ability to adjust its parameters in a more aggressive fashion. 

We propose Q to be $1.05.

#### Maximum Temperature and Bean To Max LP Scalar Changes

In order to adjust the Bean to Max LP Ratio, in practice Beanstalk adjusts a scalar (the Bean to Max LP Scalar) between 0 and 1, where 0 results in the minimum Bean to Max LP Ratio and 1 results in the maximum Bean to Max LP Ratio. Therefore, Cases V2 can adjust the Bean to Max LP Ratio independently of the minimum and maximum values.

Cases V2 updates the Maximum Temperature ($T$) and the Bean to Max LP Scalar ($L$) for the next Season $s + 1$ like so (where $m_T$ and $b_T$ are the relative and absolute changes to $T$, and $m_L$ and $b_L$ are the relative and absolute changes to $L$):

$$ T_{s+1} = max(m_T * T_s + b_T, 1) $$

$$ L_{s+1} = min(max(m_L * L_s + b_L, 0), 1) $$

The Maximum Temperature has a minimum of 1%, and range of the Bean to Max LP Scalar is $[0, 1]$.

#### Case Data

In Cases V1, only 1 parameter was stored (the absolute change in Maximum Temperature) for each case. Cases V2 stores 4 parameters for each case:
* Relative Maximum Temperature Adjustment (`mT` with 6 decimal precision, *i.e.*, 100% = `100e6`)
* Absolute Maximum Temperature Adjustment (`bT` with 2 decimal precision, *i.e.*, 100% = `1e2`)
* Relative Bean To Max LP Scalar Adjustment (`mL` with 18 decimal precision, *i.e.*, 100% = `100e18`)
* Absolute Bean To Max LP Scalar Adjustment (`bL`) with 18 decimal precision, *i.e.*, 100% = `100e18`)

This is stored as a `bytes32`, which is decoded when used. 7 bytes are left unused for potential future use.

```
struct CaseData {
    uint32 mT;
    int8 bT;
    uint80 mL;
    int80 bL;
}
```

#### 144 Cases to Rule Them All

In all 144 cases,`mT` and `mL` are set to 1, *i.e.*, there are no proposed relative adjustments to Maximum Temperature or the Bean to Max LP Scalar. As a result, each cell in the following tables strictly includes [`bT`, `bL`], *i.e.*, the absolute change in Maximum Temperature followed by the absolute change in the Bean to Max LP Scalar.

We propose the following absolute Maximum Temperature and Bean to Max LP Scalar changes in each case:

![image](https://arweave.net/o-E4GXuNl__j2-fx0L9z2x_eSgTRqafzGpOJYUhe0rA)

![image](https://arweave.net/yoWQ69osQXWPi8hECSj_R7pJoDbl44_Sf8788N95W8s)

![image](https://arweave.net/sBXoXvVx5RnbGWuNmIY2tOamVJJLYuZ-nSwiS-NaM9M)

![image](https://arweave.net/aQiUpR1saNtzo1WRTAwpBB75H1wn08Bp33C1-xyg9Fw)

### Silo V3 Unmigrated BDV

Currently, the total Deposited BDV stored on-chain does not account for Deposits that have not been migrated to Silo V3. To fix this, a two-step process was implemented. Step one was implemented in [BIP-38](https://arweave.net/Km6We3lycOcIql8-cB4_ogmwHSSt5ri01ZUM0GXGvB4). 

The second step is to calculate the remaining unmigrated BDV for each token at the BIP-38 migration block, and increment the total Deposited BDV by the difference of the unmigrated BDV and the migrated BDV between BIP excecution. 

`LibTokenSilo.incrementTotalDepositedBdv(C.TOKEN, TOKEN_UN_MIGRATED_BDV - s.migratedBdvs[C.TOKEN]);`

This second step is performed as part of this BIP.

### Unripe λ → λ Conversions

Add support for Conversions in the Silo from Unripe λ to λ. In doing so:
* Add new Convert type in `LibDataConvert`;
* Modify `LibConvert` to include the new Convert type;
* Implement `LibChop` and modify `chop(...)` and `_getPenalizedUnderlying(...)` to use `LibChop`; and
* Implement `LibChopConvert` with the `convertUnripeBeansToBeans(...)` and `getBeanAmountOut(...)` functions which wrap `LibChop` functions.

### Dewhitelist BEAN3CRV

We propose to remove BEAN3CRV from the Deposit and Minting Whitelists, and remove the BEAN → BEAN3CRV Conversion from the Convert Whitelist.

Therefore, BEAN3CRV can no longer be Deposited in the Silo or Converted to from Bean Deposits, and is no longer used in calculating a cumulative deltaB. Existing BEAN3CRV Deposits can continue to be Converted to Bean Deposits when P < 1 in the pool.

### Flood Update

Update the Flood to occur in the whitelisted Well with the most liquidity, *i.e.*, the Flood Well.

In `Weather.sol`:
* Update `handleRain` to support Cases V2 and the initialization of the Flood Well;
* Update `sop` and `calculateSop` to support the Flood Well; and
* Update `calculateSop` to use the minimum of the instantaneous reserves from Multi Flow and the current reserves in the Well. 

### Germination

Introduce a 2 Season delay for Stalk to be issued to a new Deposit. In the interim, new Deposits have Germinating Stalk. Deposits with Germinating Stalk are considered Germinating Deposits. Germinating Deposits can be Withdrawn or Transferred, but cannot be Converted.

The BDV of Germinating Deposits are not included in the calculations of Deposited BDV for the Seed Gauge System.

Deposits made in the 2 Seasons prior to the deployment of this BIP will become Germinating at the time of deployment.

### Increase Max `gm` Reward

Increase the max `gm` reward in the first available block from 100 to 250 Beans.

## Technical Rationale
    
### Seed Gauge System

Generalizing the function that updates the Grown Stalk inflation rate (`LibGauge.updateAverageStalkPerBdvPerSeason`) makes future parameter changes via governance more straightforward and sets the stage for the calculation of the parameter to be autonomized in the future.
    
Calculating the Gauge Points of a given LP token by calling its `gpSelector` allows for different implementations to be used for different whitelisted LP tokens.

Calculating the liquidity weight of a given LP token by calling its `lwSelector` allows for different implementations to be used for different whitelisted LP tokens.

### Cases V2

Cases V1 stores the cases in an `int8` array, which is too small to accommodate the new variables (`mT`, `mL` and `bL`). Cases V2 uses a `bytes32` array which packs all 4 variables in one data type. It also provides space for more variables in the future without requiring another infrastructure upgrade to the Case system. For example, the case for increasing `bT` by 3 and decreasing `bL` by 0.50 looks like:

    //                                                                     bT
    //                                                             [  mT  ][][       mL         ][       bL         ][    null    ]
    bytes32 internal constant   T_PLUS_3_L_MINUS_FIFTY = bytes32(0x05F5E1000300056BC75E2D63100000FFFD4A1C50E94E78000000000000000000);
    
Cases V2 decodes the `bytes32` value to get `mT`, `bT`, `mL` and `bL`. `bT` has a decimal precision of 2, which corresponds to the existing Maximum Temperature changes. `mL` and `bL` have 18 decimal precision in order to provide greater flexibility in future changes to the cases.
    
Reusing the Well reserves via `LibWell.getTwaReservesFromStorageOrBeanstalkPump` to calculate non-Bean liquidity and via `LibWell.getWellPriceFromTwaReserves` to calculate P reduces the cost of calling the `gm` function, and thus the reward the Beanstalk pays to incentivize it.

Using an if-else ladder in `LibLockedUnderlying` to approximate the number of Locked Beans is necessary due to the infeasibility of performing a Newtonian estimation on-chain.

By separating the Bean to Max LP Scalar from the Bean to Max LP Ratio (see `LibGauge.getBeanToMaxLpGpPerBdvRatioScaled`), governance can more easily and independently update the scalar change in each case and the minimum and maximum Bean to Max LP Ratios. Furthermore, autonomizing the setting of these parameters in the future will be easier.
            
### Silo V3 Unmigrated BDV

Updating `totalDepositedBDV` by the amount of unmigrated BDV enables the Seed Gauge System to accurately measure the L2SR.
    
### Dewhitelist BEAN3CRV

Dewhitelisting BEAN3CRV reduces the development required to implement the Seed Gauge System, as all whitelisted LP tokens now use Wells and Pumps.

### Flood Update

Generalizing the Flood, albeit to a single Well, reduces future development work associated with changing the Well in which Flood occurs.

### Germination

Using Stems to denote time Deposited in the Silo reduces additional development needed for Germination. 

`stalkEarnedPerSeason` must be non-zero in order for Germination to denote time Deposited in the Silo. A lower `stalkEarnedPerSeason` decreases the minimum Stalk growth and reduces how much Stalk that Beanstalk needs to issue. Increasing the precision of `stalkEarnedPerSeason` by 6 (`1e6`) allows for a lower minimum value that a whitelisted token needs to support Germination. 
    
Old Deposits must update their Stem precision. A Depositor's `lastStem` needs to be updated in order to properly calculate their Grown Stalk. Deposits and a Depositor's `lastStem` are automatically updated upon an interaction with the Silo (*i.e.*, no migration akin to Silo V3 is necessary).

### Other Misc. Technical Explanations

The Season Getters Facet is added to allow for more space in the Season Facet to implement the Seed Gauge System.

The Metadata Facet is upgraded to increase composability with current NFT marketplaces like OpenSea. Omitting the token icon from the metadata allows for whitelisting tokens without needing to upgrade the Metadata Facet each time.

`LibGauge`, `LibConvert`, `LibLockedUnderlying`, `LibIncentive` and `LibGerminate` are implemented as external libraries to save bytecode space in `SeasonFacet`, `UnripeFacet`, `ConvertFacet` and `SeasonGettersFacet`. 

Properly emitting ERC-1155 events during Enroots makes Deposits composable with existing ERC-1155 indexers. 

`WhitelistStatus` allows for a non-whitelisted but Deposited token to be included in `getAverageGrownStalkPerBdv` and improves the security of whitelisting and dewhitelisting assets.

## Economic Rationale

### Seed Gauge System

The ability to set `TARGET_SEASONS_TO_CATCHUP` as a function of time allows the linear component of Stalk growth from Seeds to be preserved while being able to toggle the slope of growth in an arbitrary fashion.

Given the lack of new participation in Beanstalk it is likely that the current effective `TARGET_SEASONS_TO_CATCHUP` of ~10 months is too high. Setting `TARGET_SEASONS_TO_CATCHUP` aggressively (i.e., to 6 months) compared to where it currently is in practice is a way to respond to the significant advantage older Depositors have as a result from the extended period without growth since Replant. 

Setting an aggressive `TARGET_SEASONS_TO_CATCHUP` is more prudent while most liquidity is still Unripe, rather than when it is mostly non-Unripe, because an aggressive `TARGET_SEASONS_TO_CATCHUP` lowers the opportunity cost of Withdrawing from the Silo.

Ensuring that 1 whitelisted LP token (*i.e.*, the LP token with the highest Gauge Points per BDV) is always allocated at least as many Seeds as Beans ensures that Beanstalk never incentivizes holding Beans over providing liquidity.

The ability to set an optimal distribution of Deposited LP BDV allows for the DAO to express a preference for the ideal distribution of Bean liquidity against various assets. Given the complexity of balancing a portfolio properly, this has not been autonomized and instead been left for infrequent changes via governance. 

### Cases V2

Measuring and updating the Case system to factor in the L2SR has the potential to significantly improve peg maintenance. For example, while L2SR is high, Beanstalk can be less aggressive in rewarding Seeds to LP tokens over Beans in order to encourage Conversions below peg.

By excluding Locked Beans from the Bean supply in the L2SR calculation, Beanstalk can determine its health in terms of liquidity based on only the number of Beans that can reasonably be sold. This is preferable over factoring in Beans that cannot be sold.

Price spikes makes Beans less attractive to borrow and price other assets against. Beanstalk should be more aggressive in returning the price to peg when the price reaches a particular threshold above $1. Q = 1.05 is a reasonable threshold while the Bean supply is small. It does not seem that there is a natural opposite to Q when P < 1.

By adjusting a Bean to Max LP Scalar, Beanstalk can adjust the relative incentives for holding Deposited Beans vs the Deposited LP token with the highest Gauge Points per BDV (the "Max LP").

#### 144 Cases to Rule Them All

Although technical support for relative changes to the Maximum Temperature and the Bean to Max LP Scalar are made possible in this upgrade, more research is necessary to determine when they should be used, if at all.

When P < 1 and demand for Soil is increasing or steady, Beanstalk should be less aggressive in increasing the Maximum Temperature when the debt level is high (because the debt level is high) and more aggressive when the debt level is low (because it should be willing to sacrifice more debt at the margin in exchange for better peg maintenance). This is true for all L2SR states. Otherwise, no changes are made to the Maximum Temperature changes (in all of the proposed cases, Maximum Temperature changes in same way when P > 1 and P > Q).

No matter the L2SR, when P > Q, Beanstalk should maximally incentivize Converting to LP Deposits by aggressively decreasing the Bean to Max LP Ratio. By decreasing the scalar by 0.5 each Season, the Bean to Max LP Ratio returns to the minimum value within 2 Seasons. 

When L2SR is excessively low, in all cases, Beanstalk should be similarly aggressive in incentivizing Conversions to LP Deposits by quickly reaching the minimum Bean to Max LP Ratio.

When the L2SR is reasonably low and Pod Rate is low, Beanstalk should be similarly aggressive in reaching the minimum Bean to Max LP Ratio given that, at the margin, Beanstalk would rather take on debt than lose liquidity. When the L2SR is reasonably low and Pod Rate is high, Beanstalk should gradually increase the Bean to Max LP Ratio when P > 1 (to incentivize Conversions above peg) and gradually decrease it when P < 1 (to incentivize Conversions below peg). By changing the scalar by 0.01 each Season, the Bean to Max LP Ratio can change from the maximum to the minimum value (or vice versa) within 100 Seasons.

When L2SR is reasonably high and P < 1, Beanstalk should similarly gradually increase the Bean to Max LP Ratio to incentivize Converting from LP Deposits to Bean Deposits, and vice versa when P > 1.

When L2SR is excessively high, Beanstalk should be more willing to accept a loss of liquidity rather than an increase in debt level at the margin. Therefore, it should adjust the Bean to Max LP Scalar just as it does when L2SR is reasonably high, except it can be slightly more aggressive when P < 1 and the Pod Rate is high. By increasing the Bean to Max LP Scalar by 0.02, the Bean to Max LP Ratio can change from the minimum to the maximum value within 50 Seasons.

### Dewhitelist BEAN3CRV

Beanstalk is still subject to inter-block MEV manipulation at a scale proportional to the amount of liquidity in the BEAN3CRV pool. Any volume in the BEAN3CRV pool is subject to an unnecessary trading fee in the pool, which results in worse prices for Farmers and worse peg maintenance for Beanstalk. The price of 3CRV is dependent on the worst of the prices of USDC, USDT and DAI. Therefore, Beanstalk should not continue incentivizing providing liquidity to the BEAN3CRV pool.

### Flood Update

Flood is an essential peg maintenance tool for Beanstalk. 

### Germination

Germination adds flash loan and inter-block MEV manipulation resistance to the calculation of Deposited BDV and allows the previous 10-block Vesting Period to be removed. By preventing the accrual of Earned Beans for 1 full Season, Beanstalk further disincentivizes inorganic demand.

### Increase Max `gm` Reward

Increasing the max `gm` reward in the first available block from 100 to 250 Beans increases the probability that it is called as soon as possible during periods of high usage of the Ethereum network.

## Contract Changes

### Initialization Contract

The `init` function on the following `InitBipSeedGauge` contract is called:

- [`0x6eF9cC52Eb37e0dE9592960c0c894a1000ac7dDf`](https://etherscan.io/address/0x6eF9cC52Eb37e0dE9592960c0c894a1000ac7dDf#code)

### Unripe Facet

The following `UnripeFacet` is removed from Beanstalk:

- [`0xAa8c44D0B89864b467C3776a7Dd367ff2aB6992A`](https://etherscan.io/address/0xAa8c44D0B89864b467C3776a7Dd367ff2aB6992A#code)

The following `UnripeFacet` is added to Beanstalk:

- [`0xeBD6Fc0c2d4dc3Ea131d7F14aA2f617d63Dc0F1a`](https://etherscan.io/address/0xeBD6Fc0c2d4dc3Ea131d7F14aA2f617d63Dc0F1a#code)

#### `UnripeFacet` Function Changes

| Name                                     | Selector     | Action  | Type | New Functionality |
|:-----------------------------------------|:-------------|:--------|:-----|:------------------|
| `_getPenalizedUnderlying`                | `0xa84643e4` | Replace | View | ✓                 |
| `balanceOfPenalizedUnderlying`           | `0x1acc0a47` | Replace | View |                   |
| `balanceOfUnderlying`                    | `0x1be655e8` | Replace | View |                   |
| `getLockedBeans`                         | `0x087d78b4` | Add     | View | ✓                 |
| `getLockedBeansFromTwaReserves`          | `0x7caa025f` | Add     | View | ✓                 |
| `getLockedBeansUnderlyingUnripeBean`     | `0xbfe2f3be` | Add     | View | ✓                 |
| `getLockedBeansUnderlyingUnripeBeanEth`  | `0x208c2c98` | Add     | View | ✓                 |    
| `getPenalizedUnderlying`                 | `0x6de45df2` | Replace | View | ✓                 |
| `getPenalty`                             | `0x014a8a49` | Replace | View |                   |
| `getPercentPenalty`                      | `0xbb7de478` | Replace | View |                   |
| `getRecapFundedPercent`                  | `0x43cc4ee0` | Replace | View |                   |
| `getRecapPaidPercent`                    | `0xab434eb7` | Replace | View |                   |
| `getTotalUnderlying`                     | `0xadef4533` | Replace | View |                   |
| `getUnderlying`                          | `0x9f06b3fa` | Replace | View |                   |
| `getUnderlyingPerUnripeToken`            | `0xb8a04d1b` | Replace | View |                   |
| `getUnderlyingToken`                     | `0x691bcc88` | Replace | View |                   |
| `isUnripe`                               | `0xfc6a19df` | Replace | View | ✓                 |
| `picked`                                 | `0xd3c73ec8` | Replace | View |                   |
| `addMigratedUnderlying`                  | `0x787cee99` | Replace | Call |                   |
| `addUnripeToken`                         | `0xfa345569` | Replace | Call |                   |
| `chop`                                   | `0x9a516cad` | Replace | Call | ✓                 |
| `pick`                                   | `0x13ed3cea` | Replace | Call |                   |
| `switchUnderlyingToken`                  | `0xa33fa99f` | Replace | Call |                   |

### Metadata Facet

The following `MetadataFacet` is being removed from Beanstalk:

- [`0x8aD8dfC3303469A8c2d14763199a99363bF580cc`](https://etherscan.io/address/0x8aD8dfC3303469A8c2d14763199a99363bF580cc#code)

The following `MetadataFacet` is added to Beanstalk:

- [`0x9F5ec59d13AfDb581A383D6215b717312e875Fd2`](https://etherscan.io/address/0x9F5ec59d13AfDb581A383D6215b717312e875Fd2#code)

#### `MetadataFacet` Function Changes

| Name                   | Selector     | Action  | Type | New Functionality |
|:-----------------------|:-------------|:--------|:-----|:------------------|
| `imageURI`             | `0xc20b8071` | Replace | View |                   |
| `name`                 | `0x06fdde03` | Replace | View |                   |
| `symbol`               | `0x95d89b41` | Replace | View |                   |
| `uri`                  | `0x0e89341c` | Replace | View | ✓                 |

### BDV Facet

The following `BDVFacet` is being removed from Beanstalk:

- [`0xC1Bbee46EcB6445B176F7f172F91976ADF4e21D9`](https://etherscan.io/address/0xC1Bbee46EcB6445B176F7f172F91976ADF4e21D9#code)

The following `BDVFacet` is added to Beanstalk:

- [`0xB752bfD626AD8715DE26D9bf3b3512F13632CCa1`](https://etherscan.io/address/0xB752bfD626AD8715DE26D9bf3b3512F13632CCa1#code)

#### `BDVFacet` Function Changes

| Name                   | Selector     | Action  | Type | New Functionality |
|:-----------------------|:-------------|:--------|:-----|:------------------|
| `beanToBDV`            | `0x5a049a47` | Replace | View |                   |
| `curveToBDV`           | `0xf984019b` | Replace | View |                   |
| `unripeBeanToBDV`      | `0xc8cda2a0` | Replace | View | ✓                 |
| `unripeLPToBDV`        | `0xb0c22bb1` | Replace | View | ✓                 |
| `wellBdv`              | `0xc84c7727` | Replace | View |                   |

### Convert Facet

The following `ConvertFacet` is removed from Beanstalk:

- [`0x38Dbe7445D3C51f27E41996de5b0EA19e3E47BA6`](https://etherscan.io/address/0x38Dbe7445D3C51f27E41996de5b0EA19e3E47BA6#code)

The following `ConvertFacet` is added to Beanstalk:

- [`0x8257C2EB3265640714cCF298ad208E3054EF675F`](https://etherscan.io/address/0x8257C2EB3265640714cCF298ad208E3054EF675F#code)

#### `ConvertFacet` Function Changes

| Name                  | Selector     | Action   | Type | New Functionality |
|:----------------------|:-------------|:---------|:-----|:------------------|
| `convert`             | `0xb362a6e8` | Replace  | Call | ✓                 |

### Convert Getters Facet

The following `ConvertGettersFacet` is removed from Beanstalk:

- [`0x789e37096Fb0abbD4f64A86B51D720b371853a70`](https://etherscan.io/address/0x789e37096Fb0abbD4f64A86B51D720b371853a70#code)

The following `ConvertGettersFacet` is added to Beanstalk:

- [`0x0a4121F3c4ACd9825Ed5499ACAD9fEA7a8a4eeED`](https://etherscan.io/address/0x0a4121F3c4ACd9825Ed5499ACAD9fEA7a8a4eeED#code)

#### `ConvertGettersFacet` Function Changes

| Name                  | Selector     | Action   | Type | New Functionality |
|:----------------------|:-------------|:---------|:-----|:------------------|
| `getAmountOut`        | `0x4aa06652` | Replace  | View |                   |
| `getMaxAmountIn`      | `0x24dd285c` | Replace  | View |                   |

### Enroot Facet

The following `EnrootFacet` is removed from Beanstalk:

- [`0x5CB70cf085368698198CB45D517445d4413eB695`](https://etherscan.io/address/0x5CB70cf085368698198CB45D517445d4413eB695#code)

The following `EnrootFacet` is added to Beanstalk:

- [`0x305d7c1C53817a4e5b66043e9883FB14b2005B6B`](https://etherscan.io/address/0x305d7c1C53817a4e5b66043e9883FB14b2005B6B#code)

#### `EnrootFacet` Function Changes

| Name                   | Selector     | Action  | Type | New Functionality |
|:-----------------------|:-------------|:--------|:-----|:------------------|
| `enrootDeposit`        | `0x0b58f073` | Replace | Call |                   |
| `enrootDeposits`       | `0x88fcd169` | Replace | Call | ✓                 |

### Migration Facet

The following `MigrationFacet` is removed from Beanstalk:

- [`0xbE73a5C684B1b53d7C7758B9a614Bcfdb24f822d`](https://etherscan.io/address/0xbE73a5C684B1b53d7C7758B9a614Bcfdb24f822d#code)

The following `MigrationFacet` is added to Beanstalk:

- [`0x5A3C138cDb894e6d200CCd350cdeE7404b1f3c9B`](https://etherscan.io/address/0x5A3C138cDb894e6d200CCd350cdeE7404b1f3c9B#code)

#### `MigrationFacet` Function Changes

| Name                                     | Selector     | Action  | Type | New Functionality |
|:-----------------------------------------|:-------------|:--------|:-----|:------------------|
| `balanceOfGrownStalkUpToStemsDeployment` | `0x505f43ea` | Replace | View |                   |
| `balanceOfLegacySeeds`                   | `0x1be2cfd8` | Replace | View |                   |
| `getDepositLegacy`                       | `0xa9be1acb` | Replace | View | ✓                 |
| `mowAndMigrate`                          | `0x1f4f3d55` | Replace | Call |                   |
| `mowAndMigrateNoDeposits`                | `0xaed942e9` | Replace | Call |                   |
| `totalMigratedBdv`                       | `0x2b8cde0d` | Replace | View |                   |

### Silo Facet

The following `SiloFacet` is removed from Beanstalk:

- [`0xFb33Af0Cc65d5dE71399c0A395846F53Fff76D71`](https://etherscan.io/address/0xFb33Af0Cc65d5dE71399c0A395846F53Fff76D71#code)

The following `SiloFacet` is added to Beanstalk:

- [`0x14047A7226c1e4a0dD15F69844e3771005cfa4e3`](https://etherscan.io/address/0x14047A7226c1e4a0dD15F69844e3771005cfa4e3#code)

#### `SiloFacet` Function Changes

| Name                    | Selector     | Action  | Type | New Functionality |
|:------------------------|:-------------|:--------|:-----|:------------------|
| `getSeedsPerToken`      | `0x9f9962e4` | Remove  | View |                   |
| `inVestingPeriod`       | `0x0b2939d1` | Remove  | View |                   |
| `claimPlenty`           | `0x45947ba9` | Replace | Call | ✓                 |
| `deposit`               | `0xf19ed6be` | Replace | Call | ✓                 |
| `mow`                   | `0x150d5173` | Replace | Call |                   |
| `mowMultiple`           | `0x7d44f5bb` | Replace | Call |                   |
| `plant`                 | `0x779b3c5c` | Replace | Call | ✓                 |
| `safeBatchTransferFrom` | `0x2eb2c2d6` | Replace | Call | ✓                 |
| `safeTransferFrom`      | `0xf242432a` | Replace | Call |                   |
| `transferDeposit`       | `0x081d77ba` | Replace | Call |                   |
| `transferDeposits`      | `0xc56411f6` | Replace | Call | ✓                 |
| `withdrawDeposit`       | `0xe348f82b` | Replace | Call | ✓                 |
| `withdrawDeposits`      | `0x27e047f1` | Replace | Call | ✓                 |

### Silo Getters Facet

The following `SiloGettersFacet` is added to Beanstalk:

- [`0xBbc36F691aBA133a214a8cb66Ab8847b8A3a5622`](https://etherscan.io/address/0xBbc36F691aBA133a214a8cb66Ab8847b8A3a5622#code)

#### `SiloGettersFacet` Function Changes

| Name                                         | Selector     | Action  | Type | New Functionality |
|:---------------------------------------------|:-------------|:--------|:-----|:------------------|
| `balanceOf`                                  | `0x00fdd58e` | Replace | View |                   |
| `balanceOfBatch`                             | `0x4e1273f4` | Replace | View |                   |
| `balanceOfDepositedBDV`                      | `0xbc8514cf` | Replace | View |                   |
| `balanceOfEarnedBeans`                       | `0x3e465a2e` | Replace | View | ✓                 |
| `balanceOfEarnedStalk`                       | `0x341b94d5` | Replace | View |                   |
| `balanceOfFinishedGerminatingStalkAndRoots`  | `0xc063989e` | Add     | View | ✓                 |
| `balanceOfGerminatingStalk`                  | `0x838082b5` | Add     | View | ✓                 |
| `balanceOfGrownStalk`                        | `0x8915ba24` | Replace | View |                   |
| `balanceOfPlenty`                            | `0x896651e8` | Replace | View |                   |
| `balanceOfRainRoots`                         | `0x69fbad94` | Replace | View |                   |
| `balanceOfRoots`                             | `0xba39dc02` | Replace | View |                   |
| `balanceOfSop`                               | `0xa7bf680f` | Replace | View |                   |
| `balanceOfStalk`                             | `0x8eeae310` | Replace | View |                   |
| `balanceOfYoungAndMatureGerminatingStalk`    | `0x0fb01e05` | Add     | View | ✓                 |
| `bdv`                                        | `0x8c1e6f22` | Replace | View |                   |
| `getDeposit`                                 | `0x61449212` | Replace | View |                   |
| `getDepositId`                               | `0x98f2b8ad` | Replace | View |                   |
| `getEvenGerminating`                         | `0x1ca5f625` | Add     | View | ✓                 |
| `getGerminatingRootsForSeason`               | `0x96e7f21e` | Add     | View | ✓                 |
| `getGerminatingStalkAndRootsForSeason`       | `0x4118140a` | Add     | View | ✓                 |
| `getGerminatingStalkForSeason`               | `0x9256dccd` | Add     | View | ✓                 |
| `getGerminatingStem`                         | `0xa953f06d` | Add     | View | ✓                 |
| `getGerminatingStems`                        | `0xe5b17f2a` | Add     | View | ✓                 |
| `getGerminatingTotalDeposited`               | `0xc25a156c` | Add     | View | ✓                 |
| `getGerminatingTotalDepositedBdv`            | `0x9b3ec513` | Add     | View | ✓                 |
| `getLastMowedStem`                           | `0x7fc06e12` | Replace | View |                   |
| `getLegacySeedsPerToken`                     | `0xf5cb9097` | Add     | View | ✓                 |
| `getMowStatus`                               | `0xdc25a650` | Replace | View |                   |
| `getOddGerminating`                          | `0x85167e51` | Add     | View | ✓                 |
| `getTotalDeposited`                          | `0x0c9c31bd` | Replace | View |                   |
| `getTotalDepositedBDV`                       | `0x9d6a924e` | Replace | View |                   |
| `getTotalGerminatingAmount`                  | `0xb45ef2eb` | Add     | View | ✓                 |
| `getTotalGerminatingBdv`                     | `0x9dcf67f0` | Add     | View | ✓                 |
| `getTotalGerminatingStalk`                   | `0x7d4a51cb` | Add     | View | ✓                 |
| `getYoungAndMatureGerminatingTotalStalk`     | `0x5a8e63e3` | Add     | View | ✓                 |
| `grownStalkForDeposit`                       | `0x3a1b0606` | Replace | View |                   |
| `lastSeasonOfPlenty`                         | `0xbe6547d2` | Replace | View |                   |
| `lastUpdate`                                 | `0xcb03fb1e` | Replace | View |                   |
| `migrationNeeded`                            | `0xc38b3c18` | Replace | View |                   |
| `seasonToStem`                               | `0x896ab1c6` | Replace | View |                   |
| `stemStartSeason`                            | `0xbc771977` | Replace | View |                   |
| `stemTipForToken`                            | `0xabed2d41` | Replace | View |                   |
| `tokenSettings`                              | `0xe923e8d4` | Replace | View |                   |
| `totalEarnedBeans`                           | `0xfd9de166` | Replace | View |                   |
| `totalRoots`                                 | `0x46544166` | Replace | View |                   |
| `totalStalk`                                 | `0x7b52fadf` | Replace | View |                   |

### Whitelist Facet

The following `WhitelistFacet` is removed from Beanstalk:

- [`0x730bfC44C8c51c469aFc133B0e445d0CC9FFc63d`](https://etherscan.io/address/0x730bfC44C8c51c469aFc133B0e445d0CC9FFc63d#code)

The following `WhitelistFacet` is added to Beanstalk:

- [`0x47DA294946D41E90486ca8BB2adA493A6b974A2a`](https://etherscan.io/address/0x47DA294946D41E90486ca8BB2adA493A6b974A2a#code)

#### `WhitelistFacet` Function Changes

| Name                                 | Selector     | Action   | Type | New Functionality |
|:-------------------------------------|:-------------|:---------|:-----|:------------------|
| `whitelistToken`                     | `0xd8a6aafe` | Remove   | Call |                   |
| `whitelistTokenWithEncodeType`       | `0xb4f55be8` | Remove   | Call |                   |
| `getSiloTokens`                      | `0xe9522c08` | Add      | View | ✓                 |
| `getWhitelistStatus`                 | `0xd9ba32fc` | Add      | View | ✓                 |
| `getWhitelistStatuses`               | `0x170cf084` | Add      | View | ✓                 |
| `getWhitelistedLpTokens`             | `0x9d1d2877` | Add      | View | ✓                 |
| `getWhitelistedTokens`               | `0xe26f7900` | Add      | View | ✓                 |
| `getWhitelistedWellLpTokens`         | `0x76a7bc84` | Add      | View | ✓                 |
| `dewhitelistToken`                   | `0x86b40a1b` | Replace  | Call |                   |
| `updateGaugeForToken`                | `0xce5fb821` | Add      | Call | ✓                 |
| `updateStalkPerBdvPerSeasonForToken` | `0xf18d9ed0` | Replace  | Call | ✓                 |
| `whitelistToken`                     | `0x371b5b03` | Add      | Call | ✓                 |
| `whitelistTokenWithEncodeType`       | `0x052ebc26` | Add      | Call | ✓                 |

### Gauge Point Facet

The following `GaugePointFacet` is added to Beanstalk:

- [`0x4b10DfCCD211b77671E1E01541346F0c659C681b`](https://etherscan.io/address/0x4b10DfCCD211b77671E1E01541346F0c659C681b#code)

#### `GaugePointFacet` Function Changes

| Name                                 | Selector     | Action   | Type | New Functionality |
|:-------------------------------------|:-------------|:---------|:-----|:------------------|
| `defaultGaugePointFunction`          | `0xd8317c71` | Add      | Call | ✓                 |

### Liquidity Weight Facet

The following `LiquidityWeightFacet` is added to Beanstalk:

- [`0x9Aaaa79FDDEFcBbD30605F653FA440922B327c3D`](https://etherscan.io/address/0x9Aaaa79FDDEFcBbD30605F653FA440922B327c3D#code)

#### `LiquidityWeightFacet` Function Changes

| Name                                 | Selector     | Action   | Type | New Functionality |
|:-------------------------------------|:-------------|:---------|:-----|:------------------|
| `maxWeight`                          | `0xa8b0bb83` | Add      | View | ✓                 |

### Season Facet

The following `SeasonFacet` is removed from Beanstalk:

- [`0x7667b52cbbe2D7CA54334C7c00F1396faF660DeA`](https://etherscan.io/address/0x7667b52cbbe2D7CA54334C7c00F1396faF660DeA#code)

The following `SeasonFacet` is added to Beanstalk:

- [`0xB5818dE8b02394b4300F15F61083dc3ff976EAA1`](https://etherscan.io/address/0xB5818dE8b02394b4300F15F61083dc3ff976EAA1#code)

#### `SeasonFacet` Function Changes

| Name                 | Selector     | Action  | Type | New Functionality |
|:---------------------|:-------------|:--------|:-----|:------------------|
| `curveOracle`        | `0x07a3b202` | Remove  | View |                   |
| `seasonTime`         | `0xca7b7d7b` | Replace | View |                   |
| `gm`                 | `0x64ee4b80` | Replace | Call | ✓                 |
| `sunrise`            | `0xfc06d2a6` | Replace | Call |                   |

### Season Getters Facet

The following `SeasonGettersFacet` is added to Beanstalk:

- [`0x46d11a5076EAAD1ffA24b0C2DDF38d4Aeaa19920`](https://etherscan.io/address/0x46d11a5076EAAD1ffA24b0C2DDF38d4Aeaa19920#code)

#### `SeasonGettersFacet` Function Changes

| Name                                   | Selector     | Action  | Type | New Functionality |
|:---------------------------------------|:-------------|:--------|:-----|:------------------|
| `abovePeg`                             | `0x2a27c499` | Replace | View |                   |
| `calcGaugePointsWithParams`            | `0xeedc7ab9` | Add     | View | ✓                 |
| `getAverageGrownStalkPerBdv`           | `0x7ba6cbf8` | Add     | View | ✓                 |
| `getAverageGrownStalkPerBdvPerSeason`  | `0xeb0e1215` | Add     | View | ✓                 |
| `getBeanEthGaugePointsPerBdv`          | `0xd1db56b8` | Add     | View | ✓                 |
| `getBeanGaugePointsPerBdv`             | `0x69aa7e02` | Add     | View | ✓                 |
| `getBeanToMaxLpGpPerBdvRatio`          | `0xcc88d4f9` | Add     | View | ✓                 |
| `getBeanToMaxLpGpPerBdvRatioScaled`    | `0x673c75f0` | Add     | View | ✓                 |
| `getDeltaPodDemand`                    | `0x64b3496b` | Add     | View | ✓                 |
| `getGaugePoints`                       | `0x93523425` | Add     | View | ✓                 |
| `getGaugePointsPerBdvForToken`         | `0x64887852` | Add     | View | ✓                 |
| `getGaugePointsPerBdvPerWell`          | `0xb2b0556d` | Add     | View | ✓                 |
| `getGaugePointsWithParams`             | `0x141933bf` | Add     | View | ✓                 |
| `getGrownStalkIssuedPerGp`             | `0xf98da2de` | Add     | View | ✓                 |
| `getGrownStalkIssuedPerSeason`         | `0x383f170f` | Add     | View | ✓                 |
| `getLargestLiqWell`                    | `0xd1943f7f` | Add     | View | ✓                 |
| `getLiquidityToSupplyRatio`            | `0xcb2d0a3c` | Add     | View | ✓                 |
| `getPodRate`                           | `0xcce813a1` | Add     | View | ✓                 |
| `getSeedGauge`                         | `0x6af8e5a4` | Add     | View | ✓                 |
| `getSopWell`                           | `0x7d23804d` | Add     | View | ✓                 |
| `getTotalBdv`                          | `0x50539159` | Add     | View | ✓                 |
| `getTotalUsdLiquidity`                 | `0xbbf459a7` | Add     | View | ✓                 |
| `getTotalWeightedUsdLiquidity`         | `0xf788b47c` | Add     | View | ✓                 |
| `getTwaLiquidityForWell`               | `0xa13a3742` | Add     | View | ✓                 |
| `getWeightedTwaLiquidityForWell`       | `0x93c9e531` | Add     | View | ✓                 |
| `paused`                               | `0x5c975abb` | Replace | View |                   |
| `plentyPerRoot`                        | `0xe60d7a83` | Replace | View |                   |
| `poolDeltaB`                           | `0x471bcdbe` | Replace | View |                   |
| `rain`                                 | `0x43def26e` | Replace | View |                   |
| `season`                               | `0xc50b0fb0` | Replace | View |                   |
| `sunriseBlock`                         | `0x3b2ecb70` | Replace | View |                   |
| `time`                                 | `0x16ada547` | Replace | View |                   |
| `totalDeltaB`                          | `0x06c499d8` | Replace | View |                   |
| `weather`                              | `0x686b6159` | Replace | View |                   |
| `wellOracleSnapshot`                   | `0x597490c0` | Replace | View |                   |

### Event Changes

| Name                                   | Change                                            |
|:---------------------------------------|:--------------------------------------------------|
| `WeatherChange`                        | Removed                                           |
| `TemperatureChange`                    | New event                                         |
| `UpdateAverageStalkPerBdvPerSeason`    | New event                                         |
| `GaugePointChange`                     | New event                                         |
| `BeanToMaxLpGpPerBdvRatioChange`       | New event                                         |
| `FarmerGerminatingStalkBalanceChanged` | New event                                         |
| `TotalGerminatingBalanceChanged`       | New event                                         |
| `TotalGerminatingStalkChanged`         | New event                                         |
| `TotalStalkChangedFromGermination`     | New event                                         |
| `UpdateGaugeSettings`                  | New event                                         |
| `AddWhitelistStatus`                   | New event                                         |
| `RemoveWhitelistStatus`                | New event                                         |
| `UpdateWhitelistStatus`                | New event                                         |
| `WhitelistToken`                       | Updated to include Gauge parameters               |
| `SeasonOfPlenty`                       | Added token parameter                             |
| `ClaimPlenty`                          | Added token parameter                             |

## Beans Minted

None.

## Audit

The commit hash of this BIP is [ac8e681c7daa7cb046c1e405b27e50e7e44c0504](https://github.com/BeanstalkFarms/Beanstalk/pull/722/commits/ac8e681c7daa7cb046c1e405b27e50e7e44c0504).

Cyfrin performed an audit of the initial version of the Seed Gauge system. The audit report can be read [here](https://arweave.net/OTss_ld5AJRbGPlaL6lRgzh8ZoOA5-n9HnUljvphsic).

After significant changes, such as the Germination update, an audit competition of the Seed Gauge System was held via Codehawks using commit hash [a3658861af8f5126224718af494d02352fbb3ea5](https://github.com/Cyfrin/2024-02-Beanstalk-1/commit/a3658861af8f5126224718af494d02352fbb3ea5). The final report can be read [here](https://www.codehawks.com/report/clsxlpte900074r5et7x6kh96).

Audit remediations were committed in PRs [#819](https://github.com/BeanstalkFarms/Beanstalk/pull/819), [#829](https://github.com/BeanstalkFarms/Beanstalk/pull/829) and [#831](https://github.com/BeanstalkFarms/Beanstalk/pull/831). 

#### Post Audit Changes

The following changes have been made to the Seed Gauge System code, but have not been audited.

PR [#836](https://github.com/BeanstalkFarms/Beanstalk/pull/836), which skips updating the Bean to Max LP Scalar if the Chainlink oracle fails.

PR [#837](https://github.com/BeanstalkFarms/Beanstalk/pull/837), which updates the Germination events.

PR [#843](https://github.com/BeanstalkFarms/Beanstalk/pull/843), which adds the `getLockedBeansFromTwaReserves` view function.

#### BIP-42 Bug Fix

PR [#854](https://github.com/BeanstalkFarms/Beanstalk/pull/854) fixed the Locked Bean calculation issue that was found during the BIP-42 Voting Period. The fix was reviewed by Cyfrin.
 
## Effective

Immediately upon commitment.
