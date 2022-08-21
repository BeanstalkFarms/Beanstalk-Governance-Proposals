# BFP-79: Terminology Updates

Proposed: July 5, 2022

Status: Passed

Link: [Snapshot](https://snapshot.org/#/beanstalkfarms.eth/proposal/0xf039eb180154642f58f60f3566192636d7d1a9d6c24f9ea7596ab5529c5c9f7f)

---

- [Proposer](#proposer)
- [Summary](#summary)
- [Proposal](#proposal)

## Proposer 

Beanstalk Farms

## Summary

* Update various Beanstalk verbiage at the protocol, UI, and documentation level.

## Proposal

Beanstalk Farms aims to make Beanstalk as easy as possible to understand. The farming metaphor is helpful for explaining novel concepts. Selecting clear, distinct names for each component in Beanstalk is part of that effort. In that spirit, Beanstalk Farms proposes a handful of terminology changes to Beanstalk and its associated documentation.

Most of these changes either require updates to documentation, the whitepaper and/or the Beanstalk UI. Any changes that require a change to variable names at the protocol level have a checkmark in the “Requires protocol update” column of the table.

<table>
  <tr>
   <td><strong>Previous term</strong>
   </td>
   <td><strong>New term</strong>
   </td>
   <td><strong>Requires protocol update</strong>
   </td>
   <td><strong>Rationale</strong>
   </td>
  </tr>
  <tr>
   <td>Rain
   </td>
   <td>Downpour
   </td>
   <td>✔
   </td>
   <td>It’s more intuitive to use Rain as a general term to describe Seasons where Beans mint, and Downpour represents a more extreme version of a Rainy Season.
   </td>
  </tr>
  <tr>
   <td>Season of Plenty
   </td>
   <td>Flood
   </td>
   <td>✔
   </td>
   <td>Season of Plenty isn’t necessarily a positive thing. It is a violent way in which Beanstalk reacts to extreme conditions. Flood captures that and extends the idea that Beans mint when it rains.
   </td>
  </tr>
  <tr>
   <td>N/A (Season where TWAP > 1)
   </td>
   <td>Rainy Season
   </td>
   <td>✔
   </td>
   <td>It would be helpful to have a name for Seasons where Beans mint. It is intuitive that Pods and Sprouts ripen (Beans mint) at the end of a Rainy Season.
   </td>
  </tr>
  <tr>
   <td>N/A (Season where TWAP &lt;= 1)
   </td>
   <td>Dry Season
   </td>
   <td>✔
   </td>
   <td>It would be helpful to have a name for Seasons where Beans are not minted. Dry is the natural opposite of Rainy.
   </td>
  </tr>
  <tr>
   <td>Weather
   </td>
   <td>Temperature
   </td>
   <td>✔
   </td>
   <td>In general, the Temperature increases during Dry Seasons and decreases during Rainy Seasons (also, Weather is not very intuitive as a number).
   </td>
  </tr>
  <tr>
   <td>Earned Seeds (previously Farmable Seeds on the pre-exploit UI)
   </td>
   <td>Plantable Seeds
   </td>
   <td>✔
   </td>
   <td>Earned Seeds are not “active” and thus should have a different adjective than Earned Beans/Stalk.
   </td>
  </tr>
  <tr>
   <td>Earn (previously Farm on the pre-exploit UI)
   </td>
   <td>Plant
   </td>
   <td>✔
   </td>
   <td>Earn should be specific to the Seeds it activates. Thus, Plantable Seeds must be Planted to activate them.
   </td>
  </tr>
  <tr>
   <td>Ripen (in Barn Raise context)
   </td>
   <td>Chop
   </td>
   <td>✔
   </td>
   <td>Ripen is already used in the context of Pods ripening, and the action to claim the assets underlying assets should be a more active one. Hence, Chop instead of Ripen.
   </td>
  </tr>
  <tr>
   <td>N/A (pre-exploit Stalk that has vested)
   </td>
   <td>Revitalized Stalk
   </td>
   <td>✔
   </td>
   <td>There needs to be a name for this, and Revitalized indicates that this Stalk is given new life after it was haircut.
   </td>
  </tr>
  <tr>
   <td>N/A (pre-exploit Stalk that has not vested)
   </td>
   <td>Unrevitalized Stalk
   </td>
   <td>
   </td>
   <td>The pre-exploit Stalk that a Silo Member has left to earn back.
   </td>
  </tr>
  <tr>
   <td>N/A (pre-exploit Seeds that have vested)
   </td>
   <td>Revitalized Seeds
   </td>
   <td>✔
   </td>
   <td>There needs to be a name for this, and Revitalized indicates that these Seeds are given new life after it was haircut.
   </td>
  </tr>
  <tr>
   <td>N/A (pre-exploit Seeds that have not vested)
   </td>
   <td>Unrevitalized Seeds
   </td>
   <td>
   </td>
   <td>The pre-exploit Seeds that a Silo Member has left to earn back.
   </td>
  </tr>
  <tr>
   <td>N/A (Add Revitalized Stalk and Seeds to your Stalk and Seed balances)
   </td>
   <td>Enroot
   </td>
   <td>✔
   </td>
   <td>Another word for Planting, as Silo Members must take some action to activate their Revitalized Stalk/Seeds.
   </td>
  </tr>
  <tr>
   <td>N/A (Beans/LP underlying Unripe Beans/LP)
   </td>
   <td>Ripe Beans/LP
   </td>
   <td>✔
   </td>
   <td>There needs to be a name for this, and Ripe indicates the assets that Unripe assets have a pro rata share of.
   </td>
  </tr>
  <tr>
   <td>Whitelist
   </td>
   <td>Add to Whitelist
   </td>
   <td>✔
   </td>
   <td>Add to Whitelist is more clear than using Whitelist as a verb.
   </td>
  </tr>
  <tr>
   <td>Dewhitelist
   </td>
   <td>Remove from Whitelist
   </td>
   <td>✔
   </td>
   <td>Remove from Whitelist is more clear than using Dewhitelist as a verb.
   </td>
  </tr>
  <tr>
   <td>Marketplace
   </td>
   <td>Market
   </td>
   <td>✔
   </td>
   <td>The component of Beanstalk that will house many marketplaces like the Pod Market, Deposit Market, etc.
   </td>
  </tr>
  <tr>
   <td>Farmers’ Market
   </td>
   <td>Pod Market
   </td>
   <td>
   </td>
   <td>There will be many future marketplaces at the Market (like Deposits, NFTs, etc.), so it’s best to be descriptive and use Pod Market.
   </td>
  </tr>
  <tr>
   <td>Update (No previous term on the pre-exploit UI, Farm was the function that claimed both Grown Stalk and Farmable Seeds)
   </td>
   <td>Mow
   </td>
   <td>✔
   </td>
   <td>Mow is called upon any interaction with the Silo. Grown Stalk must be Mowed to add it to the rest of your Stalk balance.
   </td>
  </tr>
  <tr>
   <td>Claim non-Deposited Unripe assets
   </td>
   <td>Pick
   </td>
   <td>✔
   </td>
   <td>Non-Deposited Unripe assets are issued through the use of a Merkle tree, so they must be Picked off the Merkle tree.
   </td>
  </tr>
  <tr>
   <td>Unfertilized Beans
   </td>
   <td>Sprouts
   </td>
   <td>✔
   </td>
   <td>Unfertilized Beans are not Beans, so it makes more sense for this to have a name akin to Pods that represents something that can ripen or grow into Beans.
   </td>
  </tr>
  <tr>
   <td>Fertilized Beans
   </td>
   <td>Fertilized Sprouts
   </td>
   <td>✔
   </td>
   <td>Applying Fertilizer to Sprouts over time yields Fertilized Sprouts that are redeemable for Beans.
   </td>
  </tr>
  <tr>
   <td>Claim Fertilized Sprouts
   </td>
   <td>Rinse
   </td>
   <td>✔
   </td>
   <td>Fertilized Sprouts become Beans after they are Rinsed.
   </td>
  </tr>
  <tr>
   <td>Wrapped/Internal Beans
   </td>
   <td>Farm Beans
   </td>
   <td>✔
   </td>
   <td>Beans stored on the Beanstalk farm are known as Farm Beans. This will be the case with other assets in Beanstalk, like Farm ETH.
   </td>
  </tr>
  <tr>
   <td>Claimable Withdrawn Beans (timer has elapsed)
   </td>
   <td>Receivable Beans
   </td>
   <td>✔
   </td>
   <td>Beans are Receivable after they go through the Withdrawal process.
   </td>
  </tr>
  <tr>
   <td>Claimable Beans + Wrapped Beans
   </td>
   <td>Farmable Beans
   </td>
   <td>
   </td>
   <td>This is the superset ofBeans that can be used on the Bean Farm in transactions. Hence, Farm<em>able</em> Beans.
   </td>
  </tr>
</table>

With this new verbiage, it is more straightforward to create a taxonomy of different states that Beans can be in:

![image](https://user-images.githubusercontent.com/28496268/185802364-937a70fb-c498-46ae-905d-fec58c23f425.png)

<table>
  <tr>
   <td><strong>Bean state</strong>
   </td>
   <td><strong>Definition</strong>
   </td>
  </tr>
  <tr>
   <td>Farm Beans
   </td>
   <td>Beans stored in Beanstalk. 
   </td>
  </tr>
  <tr>
   <td>Harvestable Pods
   </td>
   <td>Pods that are redeemable for 1 Bean each.
   </td>
  </tr>
  <tr>
   <td>Fertilized Sprouts
   </td>
   <td>Sprouts that are redeemable for 1 Bean each.
   </td>
  </tr>
  <tr>
   <td>Receivable Beans
   </td>
   <td>Withdrawn Beans after the Withdrawal timer has elapsed. 
   </td>
  </tr>
  <tr>
   <td>Circulating Beans
   </td>
   <td>Beans in Farmers’ wallets. 
   </td>
  </tr>
  <tr>
   <td>Farmable Beans
   </td>
   <td>Beans stored in Beanstalk that can be used in transactions.
   </td>
  </tr>
  <tr>
   <td>Unused Beans
   </td>
   <td>Beans that can be used within Beanstalk. These are the Beans that Farmers can use to Deposit, Order, etc.
   </td>
  </tr>
  <tr>
   <td>Withdrawn Beans
   </td>
   <td>Beans Withdrawn from the Silo before they are unfrozen.
   </td>
  </tr>
  <tr>
   <td>Ordered Beans
   </td>
   <td>Beans stored in Pod Orders. 
   </td>
  </tr>
  <tr>
   <td>Deposited Beans
   </td>
   <td>Beans Deposited in the Silo. These Beans must be Withdrawn and the Withdrawal timer must elapse in order to use them.
   </td>
  </tr>
  <tr>
   <td>Earned Beans
   </td>
   <td>Beans paid to a Silo Member since the last Season the Silo Member interacted with the Silo.
   </td>
  </tr>
  <tr>
   <td>Ripe Beans
   </td>
   <td>Beans that are minted as Fertilizer is sold and debt to Fertilizer holders is repaid. Unripe Beans represent a pro rata share of underlying Ripe Beans.
   </td>
  </tr>
</table>
