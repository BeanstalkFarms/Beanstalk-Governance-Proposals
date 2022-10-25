# BFP-77: Choose BEAN:3CRV A Parameter

Proposed: June 26, 2022

Status: Passed

Link: [Snapshot](https://snapshot.org/#/beanstalkfarms.eth/proposal/0xbb1db9c60534b7aa3951ea0d7b107f755d555acdd95c495388be7a1bd7f494e0), [Arweave](https://arweave.net/BFwBb84TV4XkpDf6jWOVi-_PlNKGVJ7sl1KsW70GasY)

---

- [Proposer](#proposer)
- [Summary](#summary)
- [Proposal](#proposal)

## Proposer

Beanstalk Farms

## Summary

* Set the A parameter on the BEAN:3CRV metapool to 1.

## Proposal

Before the exploit on April 17, 2022, Beans were trading in 3 liquidity pools—BEAN:ETH on Uniswap V2, BEAN:3CRV on Curve (A parameter of 10), and BEAN:LUSD on Curve (A parameter of 100). Beans were only minted according to the deltaB in the BEAN:ETH pool.

The A parameter in a Curve pool affects the price movement at a given sized trade, relative to the size of the liquidity pool. A higher A parameter reduces price slippage on similar sized trades compared to a lower one. For Beanstalk, a high A parameter can create inefficiencies in the Convert and Field functionality.

A high A parameter can significantly reduce the attractiveness of Converting in the Silo or Sowing Beans at a given price, as a given deltaB will result in a price much closer to peg than a constant product formula like Uniswap V2, where in A = 0. The profitability of Converting is a function of price deviations from the peg and trading fees. Until Beans are trading in a zero-fee DEX, the profitability of Convert must compensate for trading fees. Therefore, maintaining a high A parameter would likely diminish the effectiveness of Converting, and in turn its effect on peg maintenance. 

Price deviations also influence behavior in the Field, as the Weather is denominated in Beans. The lower the Bean price, the higher your real rate of return is for Sowing Beans. A high A parameter could lead to a higher Weather being necessary to attract Sowers.

The following charts assume a pool of 10000 BEAN and 10000 3CRV, with a variable A parameter. This data scales linearly (_i.e._, X BEAN and X 3CRV in the pool). As A increases, so does the deltaB of the pool given the price. The highest jump can be seen from A = 0 to A = 1, with further increases in A resulting in a lower impact.

![chart1](https://user-images.githubusercontent.com/28496268/185802161-54f44d6b-38de-4a73-9062-1639dc2b84be.png)
![chart2](https://user-images.githubusercontent.com/28496268/185802167-f8db0d44-69da-4011-a8ec-96b05a9bd0b0.png)
![chart3](https://user-images.githubusercontent.com/28496268/185802168-8fa760b7-d6ae-4837-8386-91424a8ee407.png)

Because Beanstalk was attacked shortly before generalized minting was implemented, Beanstalk has never minted Beans according to the deltaB of a Curve pool. Given that, it seems prudent to be conservative when choosing an A parameter. It is important to use the stableswap formula as a complementary tool for utility, rather than the main driver of stability. The A parameter can be changed in the future, but requires going through Curve governance.

Therefore, Beanstalk Farms proposes deploying the Replant BEAN:3CRV pool with an A parameter of 1. 

Note: Liquidity that is recapitalized by the Barn Raise will be added to this pool at the ratio that would cause the deltaB to trend towards the pre-exploit deltaB in the Replant pool. At A = 1, the price at which liquidity is added at will be higher than the pre-exploit Bean price.

You can play around with the A parameter [here](https://www.desmos.com/calculator/jzoklx8wxo) and observe how it resembles the constant product formula more and more as the A parameter decreases. More graphs and calculations related to how different A parameters would affect Beanstalk can be found [here](https://github.com/BrianOlympus/Curveswap-Stuff).
