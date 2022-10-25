# BFP-72: The Path Forward, No Haircuts

Proposed: May 24, 2022

Status: Passed

Link: [Snapshot](https://snapshot.org/#/beanstalkfarms.eth/proposal/0xb87854d7f6f40f0877a1333028eab829b213fbcce03f16f9dd3832c8a98ab99b), [Arweave](https://arweave.net/3gfgu4ZDQq1E5m8usPe2s7aF9B8dJFYg_r1MwOyr99M)

---

 - [Proposer](#proposer)
 - [Summary](#summary)
 - [Problem](#problem)
 - [Solution](#solution)
    - [No Haircuts](#no-haircuts)
    - [Vesting](#vesting)
    - [Forfeited Assets](#forfeited-assets)
 - [Fertilizer (Barn Raise tokens)](#fertilizer-barn-raise-tokens)
    - [Token standard](#token-standard)
    - [Minimum participation size](#minimum-participation-size)
    - [Bean mints and Humidity](#bean-mints-and-humidity)
    - [Artwork](#artwork)
 - [Rationale](#rationale)
 - [Notes](#notes)
 - [Glossary](#glossary)

## Proposer

Beanstalk Farms

## Summary

- Continue with the timeline of starting the Barn Raise on June 6 and Unpausing Beanstalk in early July.
- Extend the end of the Barn Raise indefinitely until all Barn Raise tokens are sold. The Barn Raise would continue while Beanstalk is Unpaused if there are unsold tokens.
- No haircuts. All Beans, BDV of LP, Stalk, and Seeds are subject to a vesting schedule.

## Problem

The [current plan approved by the DAO](https://snapshot.org/#/beanstalkfarms.eth/proposal/0xa038d205e72ae3a835995682b18adf9512777ed554c388a7caa5bc4e98d4f8e0) only allows participation in the Barn Raise until June 27, before the Unpause. A consequence of this is that if the Barn Raise recapitalizes less than $77M of the liquidity, pre-exploit Bean, LP and Pod holders would take a haircut proportional to the amount of funds raised. Before this new proposal, this seemed to be a necessary sacrifice.

Between the macroeconomic environment and the collapse of Terra, the market is incredibly weak right now. Beanstalk should be able to operate under adverse conditions if it wants to be the ubiquitous stablecoin issuer of DeFi. Instead of being conservative, and having Beanstalk shrink significantly, only to quickly grow back to the size it was prior to the attack—with the right plan, it makes sense to be aggressive, turn Beanstalk back on without any haircut, and instead let the model perform in adverse circumstances, both exogenous and endogenous.

## Solution

Rather than end the Barn Raise on June 27, let the Barn Raise run indefinitely until all the Barn Raise tokens (Fertilizer) are sold. Beanstalk would still Unpause in early July and the Barn Raise would continue running in tandem, assuming not all the Fertilizer is sold by then. Funds raised from Fertilizer sales post-Unpause will still recapitalize non-Bean liquidity.

### No Haircuts

In this new proposal, there would be no haircut for any pre-exploit Bean, LP or Pod holders. Beans, LP, Stalk and Seeds are subject to a vesting schedule. At the time of Unpause, 100% of the Beans and BDV of LP positions would be distributed in their Unripe state.

Stalk and Seeds will be distributed based on the percentage of Fertilizer sold, and the rest of Stalk and Seeds balances will vest as more Fertilizer is sold. Pods stay the same as pre-exploit.

> As an example, if 20% of Fertilizer is sold before Unpause, pre-exploit balances of:
> 
> 1000 Beans, 2000 BDV of LP, 4000 Stalk, 12000 Seeds, and 20000 Pods,
> 
> would then become the following when Beanstalk Unpauses:
> 
> 1000 _Unripe_ Beans, 2000 BDV of _Unripe_ LP, 800 Stalk, 2400 Seeds, and 20000 Pods.

### Vesting

All pre-exploit Beans and LP will become _Unripe Beans_ and _Unripe LP_ respectively upon Unpause. Unripe assets represent a pro rata share of the underlying assets minted as debt to Fertilizer holders is repaid. Ripening is the process of converting Unripe Beans into Beans, or Unripe LP into LP. Unripe assets are subject to a vesting schedule.

In the previous proposal, the vesting schedule for Unripe assets was solely based on the percentage of debt repaid to Fertilizer holders—if 20% of the debt had been repaid, 100 Unripe Beans could ripen into 20 Beans, with a penalty of 80%.

Now, Unripe assets can ripen based on the product of the two following values: the percentage of funds raised and the amount of that debt repaid.

> As an example, if 20% of total Fertilizer has sold and 30% of the debt issued for Fertilizer has been repaid, then the penalty for Unripe Beans ripening to Beans is _1 - (20% * 30%) = 94%_.
> 
> If the 100% of total Fertilizer has sold and 50% of the debt issued for Fertilizer has been repaid, then the penalty for Unripe Beans ripening to Beans is _1 - (100% * 50%) = 50%_.
> 
> These examples work the same way for Unripe LP.

The vesting for pre-exploit Stalk and Seeds is a bit different. Stalk and Seeds simply unlock based on the percentage of Fertilizer that has been sold. This makes the Silo a more attractive destination for new Silo Members early on post-Unpause. As Stalk and Seeds vest, they need to be manually claimed to become active.

> As an example, if 20% of total Fertilizer has sold before Unpause, Silo Members receive 20% of their Stalk and Seed balances upon Unpause. Once 50% of total Fertilizer has sold, Silo Members can manually claim an additional 30%, bringing their total to 50% of their pre-exploit Stalk and Seed balances.

### Forfeited Assets

As mentioned above, assets that are ripened before all Fertilizer is sold and all Fertilizer is paid back are subject to a penalty and forfeited.

In the previous proposal, forfeited assets were effectively distributed to all other Unripe asset holders such that they could end up being recapitalized in excess of their initial balances. Now that the Barn Raise will continue post-Unpause, it seems preferable that the forfeiture instead lessen the amount of funds needed to be raised in the first place [1].

> As an example, if there’s $50M to be raised from Fertilizer, and someone with 2M BDV of Unripe LP forfeits with a 50% penalty (getting 1M BDV and forfeiting a claim to 1M BDV in the process), only $49.5M more of Fertilizer has to be sold. This is because 1M BDV is forfeited, and half of that value is non-Bean liquidity, so $500k less Fertilizer needs to be sold.
> 
> The above only plays out if LP is forfeited, because it’s the liquidity that is being recapitalized by Fertilizer sales. If someone forfeits Beans, that just makes the Unripe Beans vest earlier.

## Fertilizer (Barn Raise Tokens)

### Token standard

In the previous proposal, each Barn Raise token began earning Bean mints in parallel starting at the time of Unpause. The supply of Barn Raise tokens became fixed upon Unpause, with each token earning a pro rata share of new Bean mints until repaid. ERC-721 worked well in this case.

In this new proposal, Fertilizer tokens will have different states, as each may have a different number of Beans mints owed to them. To prevent excess gas costs for computing all of these different states, Fertilizer will instead be ERC-1155 semi-fungible tokens. Fertilizer will be fungible within a Season and non-fungible across Seasons. OpenSea supports ERC-1155 tokens.

### Minimum participation size

In the previous proposal, 15,400 NFTs were going to be sold for 5000 USDC each. This was a tradeoff made given that each additional NFT has a non-zero cost to mint. Thus, the lower the NFT price, the more gas each person has to use. 5000 USDC struck a good balance between NFT price and gas efficiency.

ERC-1155 tokens require no additional gas when minting multiple tokens in the same Season. Because lowering the minimum has no effect, there will now be 77M Fertilizer for sale at 1 USDC each.

### Bean mints and Humidity

Since the Barn Raise will continue post-Unpause, it seems prudent to change the name of the interest rate for Fertilizer to distinguish it from the Weather in the Field. The interest rate on Fertilizer will be Humidity.

> As an example, 1000 Fertilizer purchased at 500% Humidity with 1000 USDC would stop earning Beans after 6000 Beans.

Like before, Beanstalk will borrow up to $77M from lenders in the Barn Raise (now in the form of 77M Fertilizer), and Fertilizer purchased before the Unpause will have a 500% Humidity.

The Humidity will change post-Unpause. During the first Season post-Unpause, the Humidity will start at 250% and decrease by 0.5% every Season until the Humidity is 20%. This will take about 20 days post-Unpause to hit the minimum Humidity of 20%.

There are 3 states Fertilizer can be in:
- If Fertilizer is not sold yet, it’s Available.
- If Fertilizer is owed Bean mints, it’s Active. Active Fertilizer receives one-third of new Bean mints.
- If Fertilizer is done earning Bean mints, it’s Used.

It’s important to consider all of these incentives in tandem. From a Humidity perspective, the concept is to reward early participation in the Barn Raise, but have the reward drop off quickly, such that demand for Soil and the Silo increases as Fertilizer becomes less attractive (both in terms of Humidity and the number of Active Fertilizer).

It is expected that if Beanstalk is successful long term, even 20% Humidity would eventually be attractive based on the number of Active Fertilizer. If there’s no Active Fertilizer and there’s still Fertilizer to be sold, someone can buy Fertilizer and earn the entire one-third of Bean mints allocated to Active Fertilizer.

The vesting schedule tied to the product of Fertilizer sold and Fertilizer paid back minimizes the new supply from vesting assets even in the instance there are extended periods of minimal demand for Fertilizer.

### Artwork

Because ERC-1155 tokens are being used, any art would have to be the same for all tokens minted in the same Season. This also means the art would be the same for all tokens minted before the Unpause.

As a result, in addition to Fertilizer (the ERC-1155 tokens), separate ERC-721 NFTs with distinct artwork will be available for mint for addresses that participate in the Barn Raise prior to Beanstalk Unpausing. The minimum participation size for getting an NFT is 1000 USDC, and these NFTs are limited to the first 10,000 transactions of at least that minimum.

Minting of the NFTs will be optional to minimize gas costs to participate in the Barn Raise.

## Rationale

This proposal:

- Leans into the strength of the mechanism that got Beanstalk to this point by not scaling the system down with a haircut;
- Makes pre-exploit Farmers whole over time; and
- Lowers the amount of capital needed to make pre-exploit Farmers whole.
- If Beanstalk Unpauses before Fertilizer sells out and Silo Members Convert from Unripe LP to Unripe Beans when P < 1, then the amount of recapitalized liquidity required to make everyone whole decreases (because the liquidity is decreasing).

Vice versa, if Beanstalk Unpauses before Fertilizer sells out and Silo Members Convert from Unripe Beans to Unripe LP when P > 1, then the amount of recapitalized liquidity required to make everyone whole increases (because the liquidity is increasing). This is acceptable as this scenario only plays out when P > 1 (a good problem to have) [2].

The forfeiture of Unripe LP during the vesting schedule also reduces the amount of liquidity that needs to be raised, and thus the amount of Fertilizer for sale.

It’s important to acknowledge that this proposed version of the Barn Raise is higher risk. The Pod Rate would be higher post-Unpause without a haircut. For example, if 25% of the Fertilizer was sold pre-Unpause, Beanstalk would Unpause with a Pod Rate 4x higher than the pre-exploit value [3].

This proposed structure doubles down on the core economic mechanism that got Beanstalk to this point. It is up to the market to decide if Beanstalk deserves to exist—and these endogenous circumstances give Beanstalk the opportunity to prove itself even more in the midst of exogenous market turmoil.

## Notes

[1] There are edge cases where it is still possible for someone to receive >100% of their pre-exploit balance. This could happen if half the amount forfeited LP exceeds the amount left to be raised from Fertilizer sales. For example, if there is no Fertilizer left to be sold and then someone forfeits their Unripe LP, anyone who doesn’t ripen until 100% of the debt to Fertilizer holders is repaid will receive >100% of their pre-exploit balance.

[2] The ceiling on the amount of Fertilizer for sale would be the amount of liquidity if all Unripe Beans were Converted to Unripe LP.

[3] The post-Unpause Pod Rate would be the inverse of the proportion of the capital raised through the Barn Raise pre-Unpause times the pre-exploit Pod Rate.

## Glossary

Unripe Beans: An ERC-20 token that entitles pre-exploit Bean holders to a pool of vesting Beans. These vesting Beans are minted as debt to Fertilizer holders is repaid.

Unripe LP: Same as Unripe Beans but for LP.

Barn Raise: Beanstalk’s ongoing effort to recapitalize its pre-exploit non-Bean liquidity.

Fertilizer: Tokens offered by Beanstalk throughout the Barn Raise that receive one-third of new Bean mints.

Humidity: The interest rate for Fertilizer. Like the Weather, this changes depending on the state of Beanstalk.

BDV: Bean-denominated value.
