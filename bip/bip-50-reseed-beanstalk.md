# BIP-50: Reseed Beanstalk

Proposed: October 1, 2024

Status: Passed

Link: [Snapshot](https://snapshot.org/#/beanstalkdao.eth/proposal/0xb5f21487caf4e7b0ddd1ac536a4165ea21eb18348987aec95e1cb56148ca916d), [Arweave](https://arweave.net/taQg7qeAqKbhBaGHAaFBAi3lxdXHv9K7iFqwM-r66pA)

Note: During the BIP-50 vote, it was noticed that the L2 TokenFacet proposed to be added to the L2 Beanstalk referenced the L1 WETH contract address instead of the Arbitrum WETH contract address. The L2 TokenFacet was redeployed at 0xf8B5Fa117f492608b8f16AAE84C69175ead6A38d using the correct WETH contract address. 

BIP-50 incorrectly listed UpdatedSeedGaugeSettings as a new event. The correct event name is UpdatedEvaluationParameters.

The text below represents BIP-50 as voted upon, without corrections.

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
- [Appendix](#appendix)

## Proposer

Beanstalk Farms, Ben Weintraub, Brendan Sanderson

Proposer Wallet: [0xf9d183af486a973b7921ceb5fdc9908d12aab440](https://etherscan.io/verifySig/258112)

## Summary

1. Upgrade Beanstalk to include the following major features:
    * Secure Beanstalk, which implements function modifiers that maintain protocol wide invariants and adds them to most write functions in Beanstalk;
    * Generalized Convert, which introduces a new Convert function that uses Pipeline to Convert a set of arbitrary (non-Unripe) Deposits to 1 Deposit (including LP → LP Converts);
    * Generalized Flood, which implements Flood in a generalized fashion such that Flood can occur in multiple Wells at once;
    * Shipments, which introduces a generalized system for allocating new Bean mints to various components of Beanstalk; and
    * Tractor, which is a peer-to-peer transaction marketplace that allows third party operators to perform Beanstalk actions on behalf of a Farmer;
2. Upgrade Beanstalk to include the following various quality of life improvements:
    * Upgrade the Solidity version from v0.7.6 to v0.8.20;
    * Implement a generalized whitelist system that supports custom oracles, Gauge Point functions and Liquidity Weight functions that do not require a diamond cut to change for a given whitelisted asset;
        * Implement a generalized Chainlink oracle system such that a Well that has a non-Bean asset whose USD price can be queried via Chainlink can be whitelisted without a diamond cut;
        * Implement a generalized Uniswap V3 oracle system such that a Well that has a non-Bean asset whose price can be queried via Uniswap V3 can be whitelisted without a diamond cut, provided that the other token in the oracle pool has a Chainlink feed (e.g., WBTC/USDC on Uniswap V3 * USDC/USD Chainlink feed);
    * Change the control variable in the Morning Auction from 2 to 0.1 to slow down the increase in the Temperature;
    * Update the default Gauge Point function (used by all whitelisted LP tokens) to adjust the Gauge Points more aggressively the farther away the current % BDV distribution is away from optimal;
    * Update the Bean to Max LP Ratio to its minimum value upon Rain;
    * Add several view functions for getting Farmer's balances;
    * Redesign App Storage to improve developer experience and future proof new additions to contract storage;
    * Move several constants that have the potential to change into contract state;
    * Redesign reentrancy protection to support Pipeline and Depot;
    * Increase Stalk decimal precision by 1e6 to prevent micro Stalk from being lost;
    * Add a `TokenTransferred` event with data on sender and receiver addresses of Farm balance transfers;
3. Migrate Beanstalk from Ethereum mainnet to Arbitrum:
    * Pause Beanstalk on L1 mainnet;
    * Remove all L1 facets except for the DiamondCutFacet, DiamondLoupeFacet, OwnershipFacet and PauseFacet;
    * Upgrade L1 Beanstalk with a new TokenFacet and L2MigrationFacet that allows Circulating Beans to be migrated to L2;
    * Run initialization scripts that transfer whitelisted LP tokens from Beanstalk to the L1 BCM to be unwrapped and bridged to L2 BCM (BEAN:3CRV is migrated to BEAN:USDC on Arbitrum);
    * Deploy L2 Beanstalk;
    * Update L2 Beanstalk with the L1 state;
    * Deploy, add liquidity to and whitelist the following Wells (see Proposed Solution for details):
        * BEAN:USDC;
        * BEAN:USDT;
        * BEAN:WBTC;
        * BEAN:wstETH;
        * BEAN:WETH; and
        * BEAN:weETH;
    * Add all facets that include the upgrades described above (excluding FundraiserFacet, LegacyClaimWithdrawalFacet, CurveFacet and MigrationFacet) to L2 Beanstalk; and
    * Unpause L2 Beanstalk.
4. Update various processes and documentation such as Hypernative, Immunefi and the Disclosures to reflect the new state of Beanstalk.

## Links

* [BIP-50 GitHub PR](https://github.com/BeanstalkFarms/Beanstalk/pull/909)
* GitHub Commit Hash: [faa0ec60a455b0afdd20ad86f28f41cbc52c2e2d](https://github.com/BeanstalkFarms/Beanstalk/pull/909/commits/faa0ec60a455b0afdd20ad86f28f41cbc52c2e2d)
* [Safe Transaction](https://app.safe.global/transactions/tx?safe=eth:0xa9bA2C40b263843C04d344727b954A545c81D043&id=multisig_0xa9bA2C40b263843C04d344727b954A545c81D043_0x19f2d9211b0fe8a005ce12f76f147a95ed8bc14d54614848e0bb60c3d2b5a0bd)

## Problem

### Secure Beanstalk

Since Replant in August 2022, there have been 5 bugs that could have led to a loss of funds (fixed in EBIPs [1](https://arweave.net/w6AuMbl8nA30Oh7d8sfRIML4pVRj6jq23XlDvUlHQoI), [4](https://arweave.net/Eb3qBsLrBdMBGtyaR3lEhK1_r_tS0HvvCgRFmKroPM8), [5](https://arweave.net/rqnIr_xLdEm4Q0ZiMqWh26lwvNmXUnMv9zu2Is4qOmM) (only Circulating assets were at risk), [10](https://arweave.net/im3PLE28EkO_eMo4fPmtcTYBJFRErxZ_44I_LWPDIB8) and [12](https://arweave.net/zAtoxAMBSIVvJTPz45nXPT0lUbgU5Krw5MJ-SWwEmSI)). In particular, the bugs that were mitigated in EBIPs 1, 10 and 12 could have led to losses of nearly all value in the Beanstalk contract.

Since the creation of the Immunefi Bug Bounty Program in [BIP-26](https://arweave.net/GnnM7WxvhttHhIKkjffhOHgAQKR6esbjNzIzlnWS8Y4), the DAO has paid [1,530,350 Beans in bounties](https://docs.bean.money/almanac/governance/beanstalk/bicm-dashboard#beans-minted), 1,322,100 of which was for bug reports concerning funds in the Beanstalk contract that were at risk.

### Generalized Convert

The effectiveness of the LP component of the Seed Gauge System is highly limited due to excessive friction between LP Converts. Currently, in order to Convert LP → LP, the source pool(s) must have negative deltaBs and the destination pool must have a positive deltaB (effectively a Pipeline LP → BEAN → LP Conversion). This is both inefficient from a gas perspective and limiting for Farmers' choices given that the effect from a peg maintenance perspective is neutral.

### Generalized Flood

Currently, Flood is only implemented to take place in a single Well. The effectiveness of Flood is substantially decreased with the introduction of several whitelisted Wells.

### Shipments

Currently, the distribution of Bean mints (allocated to the Silo, Field and Barn) is not sufficiently generalized such that it can be upgraded by changing parameters (i.e., facet changes are required).

### Tractor

Maintaining and optimizing a position in Beanstalk requires manual intervention (Mowing, Planting, Harvesting Pods and Depositing the Beans, etc.). Smaller Farmers do not necessarily have the resources to automate the execution of their Beanstalk transactions.

Existing peer-to-peer transaction marketplaces (such as Seaport) are limited in the scope of the functionality that they support. Existing generalized function call systems (i.e., smart contract accounts, Depot + Pipeline) only support the use of assets from the sender of the transaction. 

### QoL Improvements

#### Solidity Version v0.8.20

As a result of Beanstalk using Solidity v0.7.6, many QoL tools are not available to the Beanstalk development community, such as:
* Using additional methods provided in EIP-1559;
* Naming variables from mappings; 
* Using SafeMath by default (leading to overflow); and
* Using Foundry without bespoke fixes.

Whitehats and auditors are generally more familiar with Solidity v0.8.0 and higher.

#### Whitelist Upgrades

Whitelisting a Well requires new code changes to the Beanstalk contracts, which increases the friction associated with whitelisting a Well (via audits, etc.). Ideally, the DAO can whitelist a Well in the Silo without any facet changes.

#### Morning Auction Adjustment

Currently, the Temperature increases excessively fast in the first few blocks of the Morning. For example, Soil can never be purchased at a Temperature below ~27% of the Maximum Temperature given the current parameters.

#### New Default Gauge Point Function

Currently, the default Gauge Point function increments or decrements the Gauge Points for each whitelisted LP token by 1 based on whether the current BDV distribution for the LP token is greater than or less than the optimal BDV distribution. Given the maximum and minimum Gauge Point values of 1000 and 0, respectively, it can take up to 20 days ((1000/2) Gauge Points / 24 Seasons ~= 20.833 days) for the Gauge Points to "catch up" such that the Seed rewards reflect the new current vs. optimal distribution. 

For example: BEANwstETH and BEANETH currently have 1000 and 0 Gauge Points, respectively. If sufficient amounts of BEANETH Converts to BEANwstETH such that the current distribution of BEANwstETH is higher than optimal, Beanstalk will keep rewarding more Seeds to BEANwstETH than BEANETH for ~20 days even though it is no longer necessary.

#### Minimum Bean to Max LP Ratio Upon Rain

Currently, it's possible for Beanstalk to Flood before the Bean to Max LP Ratio has reached its minimum value, i.e., before Beanstalk has created the maximum incentive for Converting downwards.

#### Technical Improvements

Some Farmer balances can only be calculated by indexing events since Beanstalk deployment, resulting in either (1) thousands of calls to RPC providers each time the Beanstalk UI loads (which often gets rate limited), or (2) reliance on infrastructure like the subgraphs. Both have historically resulted in downtime for Farmers trying to view their balances.

Beanstalk currently has a number of variables set as constants in order to save gas. This increases the friction of upgrading Beanstalk by needing to redeploy the facets with only constants changed.

The current App Storage structure is difficult to extend and develop with.

Currently, Beanstalk's reentrancy protection system does not support Pipeline and Depot.

Micro Stalk can sometimes be lost during certain Silo interactions due to the current Stalk precision relative to the Seed precision. This introduced a rounding error where users had slightly less Stalk than the amount needed to Withdraw. 

Within Beanstalk, Farmers can transfer assets to and from their Circulating and Farm balances. When a Farmer transfers an asset from their Farm Balance to another address' Farm Balance, the emitted events make it difficult to trace the origin of the assets. 

### L2 Migration to Arbitrum

Beanstalk is a gaseous protocol. While the costs of interacting with Beanstalk at the time of writing in September 2024 are reasonable, activity on Ethereum L1 is currently low. In the past when Gwei has spiked, interacting with Beanstalk has cost tens or hundreds of US dollars. This prices out smaller Farmers and reduces the efficacy of Beanstalk's peg maintenance mechanism.

Upgrades like Secure Beanstalk, the new view functions, etc. increase the cost of interacting with Beanstalk across the board.

#### New Whitelist

Opportunities for Bean liquidity providers to receive Stalk and Seed rewards are currently limited. A wider variety of options increases utility for Silo Depositors, particularly with the combination of the Seed Gauge System and Generalized Convert.

### Process Updates

Hypernative, the Immunefi Bug Bounty Program and the Beanstalk DAO Disclosures can all be updated to reflect the new state of Beanstalk.

## Proposed Solution

### Secure Beanstalk

We propose the implementation of system-wide invariants that are checked on every write call to Beanstalk. The invariants are indicative of a healthy state of Beanstalk's internal accounting and must always be maintained. These invariants ensure that the Beanstalk contract contains enough assets to cover all of its user obligations, ERC-20s are not being transferred unexpectedly and that the Bean supply is not being manipulated.

The Invariable contract defines several invariant modifiers that are used across the code base.

#### Specification

The invariants fall into three different categories:

1. ERC-20 balance checking

The `fundsSafu` modifier checks every whitelisted ERC-20 token. If the balance of the token in the Beanstalk contract is lower than the amount of the token that users can claim (through Withdrawals, Harvests, Claims, etc) then the invariant will fail and the call will revert. This ensures there are always enough tokens in the contract for Beanstalk to pay its existing obligations. Excluding diamond management functions, this invariant can be placed on every write function in Beanstalk.

2. Asset movement limitations

The `noNetFlow`, `noOutFlow`, and `oneOutFlow` invariants track the balance of ERC-20 tokens inside Beanstalk and fail if there is an unexpected movement of assets. This ensures that there are not any withdrawals of unexpected assets in the modified functions, however, it does not set any limits on the amounts withdrawn. `noNetFlow` requires that Beanstalk token balances are the same at the end of the call as they were at the beginning, `noOutFlow` requires that all Beanstalk token balances are the same or higher at the end of the call and `oneOutFlow` allows for only one asset to decrease in amount during the call. These three modifiers cover the vast majority of use cases and one of the three can be put on almost every write function in Beanstalk. Notable exceptions are diamond management, Convert and `farm`-like functions.

3. Bean supply control

The `noSupplyChange` and `noSupplyIncrease` invariants keep track of the total supply of Bean at the beginning and end of the call to ensure that the Bean supply was not unexpectedly modified. `noSupplyChange` prevents both minting and burning, whereas `noSupplyIncrease` only prevents minting. One of these two modifiers can be placed on every write function in Beanstalk that is not intentionally minting or burning Beans (such as Sowing and `gm`). Notable exceptions are diamond management and `farm`-like functions.

We also propose to remove various dead code such as the Pod Market V2 pricing functions.

### Generalized Convert

We propose a Generalized Convert implementation with support for LP → LP Converts, a Stalk penalty system to enable Converting against peg and a per-block Convert cap mechanism to protect against flash loan attacks.

The PipelineConvertFacet exposes the new `pipelineConvert` function and Stalk penalty logic has been applied to the existing ConvertFacet. The existing ConvertFacet retains support for Unripe Converts, which are not supported by the new PipelineConvertFacet.

#### Specification

The Pipeline Convert function is defined as:

```solidity
    function pipelineConvert(
        address inputToken,
        int96[] calldata stems,
        uint256[] calldata amounts,
        address outputToken,
        AdvancedPipeCall[] memory advancedPipeCalls
    )
        external
        payable
        fundsSafu
        nonReentrantFarm
        returns (int96 toStem, uint256 fromAmount, uint256 toAmount, uint256 fromBdv, uint256 toBdv);
```

Usage of the first four parameters is the same as the existing `convert` function (specify the input crates using the Deposited Stem and amounts, as well as the input and output tokens to Convert from and to). The `advancedPipeCalls` parameter is new.

The `pipelineConvert` function withdraws the corresponding whitelisted tokens from Beanstalk and passes them to the Pipeline contract. The supplied Pipeline calls should call other contracts to swap (the total input token amount available to swap can be calculated by summating all the amounts passed in via `amounts`).

#### Example Pipeline Calls for Convert

A typical series of Pipeline calls might follow these steps (using Solidity):

1. Approve spending the ERC20 token:
```solidity
bytes memory approveEncoded = abi.encodeWithSelector(
    IERC20.approve.selector,
    TARGET_ADDRESS,
    MAX_UINT256
);
```
The previous user of the Pipeline contract may not have setup an approval for spending the input token used to Convert, so an approval call verifies the ERC-20 can be sent to the `TARGET_ADDRESS` (in some cases, perhaps a Well).

2. Perform the swap:
```solidity
bytes memory addLiquidityEncoded = abi.encodeWithSelector(
    IWell.addLiquidity.selector,
    tokenAmountsIn, // tokenAmountsIn
    minAmountOut, // min out (should not be left as zero, or the Convert can be frontrun)
    PIPELINE, // recipient
    type(uint256).max // deadline
);
```

This example adds liquidity to a Well (assuming this is a Bean → Well convert). Note that this action is performed from the Pipeline contract itself, and thus, the recipient is the Pipeline contract. The swap call does not have to use Wells—other DEXs (Uniswap, Curve, etc) or aggregators (0x, 1inch, etc) can be used.

The above encoded calls are then wrapped in `AdvancedPipeCall` so that the target contract and Clipboard data can be specified. The final array of these calls are inserted into the Convert function. Note these calls may want to check available Convert capacity first if a MEV protection RPC is not used (see Convert Capacity below).

After the final Pipeline call is performed, the `pipelineConvert` function calls the ERC-20 `balanceOf` function on the Pipeline contract and removes all of the ERC20 token of the `outputToken` type. Any other ERC-20's remaining in Pipeline after the Convert must be removed by the user.

#### Stalk Penalty

Generalized Convert introduces the ability to Convert against peg and a Stalk penalty for doing so. The penalty applies a 100% reduction in Grown Stalk of the Converted Deposit based on the amount that was Converted against peg.

For example, if 20 BDV is Converted, and only 10 of the BDV Converted is against peg, then 50% of the Grown Stalk associated with Deposit will be burned. No penalty is applied if the Convert brings the cumulative deltaB closer to 0.

#### Convert Capacity

To prevent flash loan attacks that allow Converting against peg without incurring the Stalk penalty, a Convert capacity mechanism is introduced. 

Every Convert updates the amount of BDV Converted per block on a per Well and overall basis. The total capacity available before a penalty applies is based on deltaB for each corresponding Well and the overall deltaB.

View functions are exposed to read the remaining capacity for a block in order to ensure that the Pipeline function can revert if conditions become unfavorable to the user over the course of the block.

##### Convert Capacity View Functions

Two functions are available to view available Convert capacity for the block, the first being the overall capacity remaining:

```solidity
function getOverallConvertCapacity() external view returns (uint256);
```

And the second being the capacity available on a per Well basis:

```solidity
function getWellConvertCapacity(address well) external view returns (uint256);
```

If the available Convert capacity of either is less than the BDV of the amount being Converted, then the Stalk penalty will apply.

### Generalized Flood

We propose a Generalized Flood implementation that supports Flooding in the highest deltaB Wells until the cumulative deltaB is 0.

While previously the entire Pod Line became Harvestable upon Flood, we propose that only 0.1% of the Bean supply worth of Pods become Harvestable.

### Shipments

We propose a dynamic system for distributing Bean mints to the different outputs within the system—the Silo, Field and Barn. The system is sufficiently generalized such that it can change between different configurations without requiring contract changes. It can support multiple Fields and is expandable to handle unknown output types that can be implemented in future upgrades.

#### Specification

Each distribution stream for new Bean mints is defined by two different structures: a `ShipmentRoute` and a `ShipmentPlan`.

Shipment Routes define where a portion of the Bean mints will be sent to and how the associated Plan is retrieved. The Routes are stored in Beanstalk state. They can be modified via owner-restricted write functions. The Route contains an enum indicating which output stream the mints are associated with—the Silo, Field or Barn. Implementation of streams that do not fall into these categories require modification to the Beanstalk contracts.

```solidity
struct ShipmentRoute {
    address planContract;
    bytes4 planSelector;
    ShipmentRecipient recipient;
    bytes data;
}
```

Shipment Plans specify the ratio and the maximum Beans that a Route can receive for the current mint. This configuration is dynamic, based on current state and arbitrary logic. Shipment Plans are retrieved by calling standardized Plan getter functions. The functions can live internal or external to Beanstalk. Externalizing these functions allows for them to be updated without modifying Beanstalk contracts.

```solidity
struct ShipmentPlan {
    uint256 points;
    uint256 cap;
}
```

Each Plan assigns points to its associated route. The Route receives the ratio of minted Beans equal to the ratio of Route points / all points. In this way, there are not circular dependencies for each Plan to know about the other Plans.

If Beans to a route exceed the maximum Beans that Route can handle the excess Beans will be distributed to the other routes, in proportion to their points. The Silo Route should never have a cap.

Both Shipment Routes and Plans can be updated via the `setShipmentRoutes` function, in conjunction with an external contract that contains the associated Shipment Plan getters.

### Tractor

We propose Tractor, a peer-to-peer transaction marketplace that allows third party operators to perform Beanstalk actions on behalf of a Farmer.

Blueprints are off-chain data structures that are [EIP-712](https://eips.ethereum.org/EIPS/eip-712) signed to verify the intent of the publishing Farmer. Blueprints allow Farmers to define arbitrary sequences of on-chain function calls, which can be executed permissionlessly by other Farmers known as Tractor Operators.

Blueprints contain the Publisher, an Advanced Farm function call containing an arbitrary sequence of internal function calls, a set of copy instructions that define how to interpret Operator calldata, expiry parameters and the EIP-712 signature from the Publisher. Any Tractor Operator can execute any Blueprint with any calldata at anytime through the `tractor(...)` function provided that the Blueprint does not revert.

Junctions are contracts that are contain basic operations as external functions that are used by Tractor orders. Junctions allow Farmers to define Blueprints that will fail under a predefined set of conditions, such as balance limits, price thresholds, etc.

#### Examples

- A Farmer creates a Blueprint for an Operator to Plant on their behalf any time they have more than 100 Earned Beans and will pay the caller 1 Earned Bean.
- A Farmer creates a Blueprint for an Operator to Mow on their behalf any time that P > 1 and will pay the caller 5 USDC.

#### Specification

##### Blueprints

Blueprints are off-chain data structures that are EIP-712 signed to verify Publisher intent. Each Blueprint contains an arbitrary sequence of internal and external function calls wrapped into an AdvancedFarm call and to be executed through the TractorFacet. Any properly signed Blueprint can be executed through Tractor given that:

1. `startTime < block.timestamp < endTime`;
2. `blueprintNonce[nonce] < maxNonce`; and
3. The `advancedFarm` function call does not revert (Publishers can encode logic checks that revert under arbitrary conditions).

Blueprints are defined by the following struct:

```solidity
struct Blueprint {
    address publisher;
    bytes data;
    bytes32[] operatorPasteInstrs;
    uint256 maxNonce;
    uint256 startTime;
    uint256 endTime;
}
```

Blueprints are wrapped in a Requisition, which contains the Blueprint hash and the Publisher's signature of the hash.

```solidity
struct Requisition {
    Blueprint blueprint;
    bytes32 blueprintHash;
    bytes signature;
}
```

Where:

1. `publisher` is the account that published the Blueprint;
2. `data` is bytes that decode into `(AdvancedFarmCall[])`;
3. `operatorPasteInstrs` are a set of instructions that define how operator-defiend data is injected into the AdvancedFarmCalls of the `data`;
4. `maxNonce` is the maximum # of times a Blueprint can be executed;
5. `startTime` is the timestamp at which the Blueprint can start to be executed;
6. `endTime` is the timestamp at which the Blueprint can no longer be executed;
7. `blueprintHash` is the keccak256 hash of the populated Blueprint struct; and
7. `signature` is the Publisher's EIP-712 signature of the Blueprint Hash.

##### Blueprint Hash

Blueprint Hashes are unique identifiers for Blueprints. Blueprint Hashes are used to track the nonce of a Blueprint and used in the signing process. A Blueprint Hash must be hashed following the EIP-712 standard. Beanstalk provides a public helper function `getBlueprintHash(Blueprint blueprint)`.

##### Publish Blueprint

Requisitions do not need to be written on-chain. They can be provided to operators off-chain and verified via the signature. However, Tractor offers a system to publicly publish any Requisition via the `PublishRequisition` function, which emits an event containing the Requisition.

```solidity
function publishRequisition(LibTractor.Requisition calldata requisition) external;

event PublishRequisition(LibTractor.Requisition requisition);
```

##### Cancel Blueprint

Blueprints can be canceled at anytime by the Blueprint Publisher by calling `cancelBlueprint`. This sets the nonce to `uint256.max`, rendering the Blueprint unusable.

```solidity
function cancelBlueprint(LibTractor.Requisition calldata requisition) external;

event CancelBlueprint(bytes32 blueprintHash);
```

##### Tractor

```solidity
function tractor(LibTractor.Requisition calldata requisition, bytes memory operatorData) external returns (bytes[] memory results);

event Tractor(address indexed operator, bytes32 blueprintHash);
```

Tractor executes a Blueprint. Executing a Blueprint:

1. Verifies the Blueprint signature is correct;
2. Checks `startTime < block.timestamp < endTime`;
3. Checks `blueprintNonce[blueprintHash] < maxNonce`;
4. Increments `blueprintNonce[blueprintHash]`;
5. Modifies `blueprint.data` using the `operatorPasteInstrs` and `operatorData`;
6. Executes `advancedFarm` using `data`; and
7. Emits a `Tractor` event.

##### Tractor Storage

Instead of adding new state variables to `AppStorage`, a `TractorStorage` library is created where `TractorStorage` is loaded at slot `keccak256("diamond.storage.tractor")` (stored as a constant).

```solidity
struct TractorStorage {
    mapping(bytes32 => uint256) blueprintNonce;
    mapping(address => mapping(bytes32 => uint256)) blueprintCounters;
    address activePublisher;
}
```

##### Tractor Counters

Blueprints can utilize arbitrary counters to track and limit use. These are stored in TractorStorage and writes are restricted based on the Publisher's address. Blueprints using counters are expected to generate sufficiently random counter IDs to avoid collisions with a Publisher's other counters.

##### Active Publisher

Tractor performs actions on behalf of the Publisher of a Blueprint that that is executed by Tractor. Rather than using `msg.sender` to determine the account to act on, the active Publisher address is used.

### QoL Improvements

#### Solidity Version v0.8.20

We propose to upgrade each contract and facet in Beanstalk from v0.7.6 to v0.8.20.

#### Whitelist Upgrades

We propose to upgrade the whitelisting system such that various Implementations can be set and changed for each Well. Implementations can be used to the set a Well's:
* Gauge Point function selector; 
* Liquidity Weight function selector; and
* Oracle implementation for calculating the value of the non-Bean token in the Well.
 
In order for a whitelisted Well to be included in the Minting Whitelist, the Gauge System and Converts, the non-Bean token in the Well must have an Oracle Implementation. Whitelisted Wells without a Gauge Point or Liquidity Weight Implementation use the default Gauge Point and Liquidity Weight Implementation defined in GaugePointFacet and LiquidityWeightFacet, respectively.

An Implementation is defined in App Storage as: 
```
struct Implementation {
    address target;
    bytes4 selector;
    bytes1 encodeType;
    bytes data;
}
```
Where:
* `target` is the the address in which `selector` is called at; 
* `selector` is the function selector Beanstalk is calling at `address`;
* `encodeType` determines the function signature Beanstalk should call at the `address`; and
* `data` is arbitrary data that can be added to a function call.

For a given Gauge Point Implementation, the function should match the following signature: 
```
function gaugePointFunction(
    uint256 currentGaugePoints,
    uint256 optimalPercentDepositedBdv,
    uint256 percentOfDepositedBdv,
    bytes memory
)
```

For a given Liquidity Weight Implementation, the function should match the following signature: 
```
function liquidityWeight(bytes)
```

For a given Oracle Implementation, the function should match the following signature:
```
function oracle(
    uint256 tokenDecimals
    uint256 lookback
    byte memory data
)
```

Gauge Point and Liquidity Weight Implementations are set per whitelisted Well. Oracle Implementations are set on a token basis. 

Beanstalk has 2 default Oracle Implementations, which are used when the whitelisted Well is whitelisted with an oracle encodeType of `0x01` and `0x02`:
* `0x01` → Chainlink Implementation; and
* `0x02` → Uniswap and Chainlink Implementation. 

To use the `0x01` default Implementation, the Oracle Implementation address for the non-Bean token in the Well should be set to the Chainlink aggregator that the price should be fetched from. The Oracle Implementation should return the USD price of the non-Bean token.

To use the `0x02` default Implementation:
* The Oracle Implementation address for the non-Bean token in the Well should be set to the address of the Uniswap V3 pool that the price should be fetched from; and
* The Oracle Implementation address for the other token in the Uniswap V3 pool should be set to the Chainlink aggregator that the price should be fetched from.

For example, for a BEAN:WBTC Well, the `0x02` default Oracle Implementation could be used by combining the WBTC:USDC Uniswap V3 pool with the USDC:USD Chainlink aggregator.

Additional support for different function signatures can be accomplished by implementing new code for a given encode type.

#### Morning Auction Adjustment

We propose to change the control variable in the Morning Auction from 2 to 0.1.

More specifically, change the calculation for the Temperature during blocks 1 to 25 ($x$) of each Season from (the blue line):

$$ \log_{25(2) + 1}\left(2x+1 \right)$$

To the following (the red line):

$$\log_{25(0.1) + 1}\left(0.1x+1\right)$$

See [Desmos link](https://www.desmos.com/calculator/hsplkhgaaq).

![](https://arweave.net/_hYWMFMKdeUt-LKj9itcxteMBuAPaSO2pVzqqj69-a4)

Due to the L2 Migration, in practice the Temperature only updates `L1_BLOCK_TIME / L2_BLOCK_TIME = 12/0.25 = 48` times. The duration of the Morning is still 5 minutes. Thus, on Arbitrum, the Temperature during the first 1200 blocks of each Season is calculated as:

$$\log_{25(0.1) + 1}\left(0.1 \left\lfloor \frac{x}{48} \right\rfloor + 1\right)
$$

#### New Default Gauge Point Function

We propose to update the default Gauge Point function to change the Gauge Points for the whitelisted LP token by ±5/3/1 based on the following thresholds:
- Extremely Far below optimal: 
    * current % < (optimal % * (100% - 66%)) → **+5 Gauge Points**
- Relatively Far below optimal: 
    * (optimal % * (100% - 66%)) < current % < (optimal % * (100% - 33%)) → **+3 Gauge Points**
- Relatively Close below optimal:
    * (optimal % * (100% - 33%)) < current % < optimal % → **+1 Gauge Point**
- Relatively Close above optimal
    * optimal % < current % < (optimal % + (100% - optimal %) * 33%) → **-1 Gauge Point**
    * -1 Gauge Point
- Relatively Far above optimal:
    * (optimal % + (100% - optimal %) * 33%) < current % < (optimal % + (100% - optimal %) * 66%) → **-3 Gauge Points**
- Extremely Far above optimal:
    * (optimal % + (100% - optimal %) * 66%) < current % → **-5 Gauge Points**

For example, if BEANETH's optimal % is 16%, the thresholds are:
- Extremely Far below optimal: current % < 5.44%
- Relatively Far below optimal: 5.44% < current % < 10.72%
- Relatively Close below optimal: 10.72% < current % < 16%
- Relatively Close above optimal: 16% < current % < 43.72%
- Relatively Far above optimal: 43.72% < current % < 71.44%
- Extremely Far above optimal: 71.44% < current %

#### Minimum Bean to Max LP Ratio Upon Oversaturation

We propose to change `beanToMaxLpGpPerBdvRatio` to the minimum value upon Rain (i.e., at `startRain`).

#### Technical Improvements

##### View Functions

Beanstalk now stores the list of Deposit IDs and Plot indexes on each account, as well as a mapping from Deposit ID to its associated index to support a O(1) read cost. 

```
struct DepositListData {
    uint256[] depositIds;
    mapping(uint256 => uint256) idIndex;
}

...

uint256[] plotIndexes;
mapping(uint256 => uint256) piIndex;
```

The list of Deposit IDs are stored on a per token basis. 

Deposit data can be fetched using the following functions:
* `getDepositsForAccount`;
* `getTokenDepositsForAccount`;
* `getTokenDepositIdsForAccount`; and
* `getIndexForDepositId`.

Plot data can be fetched using the following functions:
* `getPlotIndexesFromAccount`;
* `getPlotsFromAccount`; and
* `balanceOfPods`.

##### Constants to State

We propose to migrate various parameters that were previously set to constants to be read from contract state. This is stored in the system storage as `EvalutationParameters` like so:

```
struct EvaluationParameters {
    uint256 maxBeanMaxLpGpPerBdvRatio;
    uint256 minBeanMaxLpGpPerBdvRatio;
    uint256 targetSeasonsToCatchUp;
    uint256 podRateLowerBound;
    uint256 podRateOptimal;
    uint256 podRateUpperBound;
    uint256 deltaPodDemandLowerBound;
    uint256 deltaPodDemandUpperBound;
    uint256 lpToSupplyRatioUpperBound;
    uint256 lpToSupplyRatioOptimal;
    uint256 lpToSupplyRatioLowerBound;
    uint256 excessivePriceThreshold;
    uint256 soilCoefficientHigh;
    uint256 soilCoefficientLow;
    uint256 baseReward;
}
```

##### App Storage Redesign

App Storage has been redesigned such that two structs are implemented: 
* `Account` contains state stored on an account level; and
* `System` contains state stored on a protocol level.

```
struct AppStorage {
    mapping(address => Account) accts;
    System sys;
}
```

Various deprecated state variables are removed between the two structs and state is reordered to improve organization and developer experience. Buffers are placed throughout the state to allow for future upgradability without the need to append new state at the end of the `AppStorage` struct.

##### Reentrancy Design

The current reentrancy system does not support `farm` and `farm`-like functions (such as `advancedFarm` and `tractor`), as the `farm` function is used within Beanstalk to call multiple functions in one transaction. This allows Farm calls to be called within other Farm calls, which may introduce additional security risks.

We propose a reentrancy redesign that now supports `farm` and `farm`-like calls. A new modifier `nonReentrantFarm` is added that allows for the function to call any other function, other than other functions that have the `nonReentrantFarm` modifier. 

##### Micro Stalk Bug

We propose to increase the precision of Stalk by 1e6 (for a total of 16 decimals) and update various functions that calculate Stalk to account for the new precision.   

##### Farm Balance Transfer Event

We propose a new event that is emitted when `transferToken` is called that explicitly includes the sender and recipient addresses:
```
event TokenTransferred(
        address indexed token,
        address indexed sender,
        address indexed recipient,
        uint256 amount,
        From fromMode,
        To toMode
    );
```

### L2 Migration to Arbitrum

We propose to migrate Beanstalk and its state from Ethereum L1 to Arbitrum.

See the Effective section for full process. Note that BEAN:3CRV liquidity is proposed to be migrated to a BEAN:USDC Well on Arbitrum.

As part of the migration process:
* Grown Stalk is Mown;
* Earned Beans are Planted; and
* Rinsable Sprouts, Unpicked Unripe assets and unclaimed Silo V2 Withdrawals are put into the respective accounts' Farm Balances.

Circulating Beans at the time of the Reseed need to be manually migrated by calling `migrateL2Beans` on L1, which burns the Beans on L1 and issues them on the L2. 

Beanstalk assets (Deposits, Plots, Beanstalk-related Farm balances, and Fertilizer) belonging to smart contract accounts cannot be automatically migrated, given that Beanstalk cannot assume an owner of a smart contract is able to gain ownership of the same address on L2. Smart contract owners will need to perform a 2 step migration process in order to claim their assets:
1. On the L1 Beanstalk contract, delegate a L2 address to be the receiver of the assets (this can be an EOA or another smart contract) by calling `approveL2Receiver`; and
2. On the L2 Beanstalk contract, the user can claim their assets by calling `issueDeposits`, `issuePlots`, `issueInternalBalances`, and `issueFertilizer` with the list of assets and a merkle proof verifying the validity of the list. 

Smart contract owners can migrate their Beanstalk assets to an EOA prior to the deployment of this BIP if they'd like they assets to be automatically migrated.

The following code is updated to support L2 deployments: 

The `gm` incentive is generalized to support L2 deployments by changing the incentive to be based on the time elapsed from the start of the hour. The incentive starts at 1 Bean and compounds 1.0201% every additional 2 seconds that elapse past the first timestamp in which `gm` could have been called, for 300 seconds.

#### New Whitelist

We propose to whitelist the following Wells with Upgradable Well Implementations (owned by Beanstalk) on Arbitrum:

| Well        | Well Function       | Optimal LP BDV Distribution | Initial Gauge Points | Liquidity Weight | Oracle Implementation             |
|:------------|:--------------------|:----------------------------|:---------------------|:-----------------|:----------------------------------|
| BEAN:USDC   | Stable 2            | 12%                         |  1000                | 100%             | Chainlink (USDC/USD)              |
| BEAN:USDT   | Stable 2            | 12%                         |  1000                | 100%             | Chainlink (USDC/USD)              |
| BEAN:WBTC   | Constant Product 2  | 20%                         |  1000                | 100%             | Chainlink (WBTC/USD)              |
| BEAN:wstETH | Constant Product 2  | 26%                         |  0                   | 100%             | Chainlink (wstETH/ETH * ETH/USD)  |
| BEAN:WETH   | Constant Product 2  | 16%                         |  1000                | 100%             | Chainlink (WETH/USD)              |
| BEAN:weETH  | Constant Product 2  | 14%                         |  1000                | 100%             | Chainlink (weETH/WETH * WETH/USD) |

### Process Updates

We propose to setup Hypernative on Arbitrum with the same configuration proposed in [BIP-46](https://arweave.net/2enPPzc5mkN18bXnApmCJNRkxwYc-CiCsU71XPualj4).

We propose to update the Immunefi Bug Bounty Program to reflect the changes proposed in this BIP, notably adding the Stable 2 Well Function and Tractor Junctions as in scope and adding following Impacts as in scope (the updated program can be read [here](https://arweave.net/IHajdEOSH3UcCy4gEUaSiEqI17-pCHCl_no3zbC8LPU)):
* Medium: Invariant is missing on a function where it should be implemented; and
* Medium: Exploit is possible but is exclusively prevented by an invariant.

We propose to update the Beanstalk DAO Disclosures to include risks associated with Hypernative, Arbitrum and the new set of whitelisted assets. The updated Beanstalk DAO Disclosures can be read [here](https://arweave.net/T1oQYQu8YSJjRCSwVDatIMIgnaJLKmQrAff70fazPn0).

## Technical Rationale

### Secure Beanstalk

Calling `farm` within other `farm`-like functions (including `advancedFarm` and `tractor`) is disabled in the interest of security with minimal to no loss in composability.

Given that packing is no longer necessary for saving gas due to the L2 migration, many variables in the Beanstalk contracts are casted up to `int256` or `uint256` to prevent unnecessary overflows.

Removing dead code reduces the surface area of potential vulnerabilities in Beanstalk.

### Generalized Convert

Unripe Converts cannot be supported by `pipelineConvert` because the underlying tokens are not liquid. For this reason, the existing `convert` function that supports Unripe Converts is left in the ConvertFacet.

Pipeline Converts are limited to usage of Pipeline (i.e. `pipeCalls`) rather than `farm` calls for security reasons (with regard to reentrancy attacks).

### Generalized Flood

In `LibWhitelist`, Wells are flagged as Floodable (`isSoppable`) based on whether or not Beanstalk should sell Beans in the Well during Flood conditions. This functionality is added in the event that future whitelisted Wells should not be Flooded in.

### Shipments

Within a given Season, if a Shipment gets paid off, the remaining Bean mints are allocated equally to all remaining Shipments until the following Season. In this way, there are not circular dependencies for each Plan to know about other Plans. Doing so with enough Shipments would eventually exceed the gas limit.

Externalizing the Shipment functionality allows the Bean mint distribution to be changed without a diamond cut.

### Tractor

All write functions functions in Beanstalk use `LibTractor.getUser` to get either `msg.sender` or the Publisher of the executing Blueprint. From a developer experience perspective, this is preferable to having a Tractor-specific version of every write function.

### QoL Improvements

#### Solidity Version v0.8.20

Most DeFi protocols use Solidity v0.8.0 or higher. Upgrading Solidity versions allows the Beanstalk development community to take advantage of tooling that is developed with v0.8.0^ in mind. For example, using Foundry with v0.7.6 requires a number of workarounds in order to properly use it. While there are more recent versions of Solidity, Beanstalk should strike a balance between using a battle tested compiler and the latest version. For reference, Basin is developed in v0.8.20.

#### Whitelist Upgrades

Removing friction associated with whitelisting new assets and modifying existing whitelisted assets allows the DAO to upgrade the whitelist more quickly and with fewer security risks.

#### Morning Auction Adjustment

Using an if-else ladder in `LibDibbler` to approximate the Temperature during the Morning is necessary due to the infeasibility of calculating logarithms on-chain.

The if-else ladder is limited to updating the Temperature every 12 seconds due to the contract size limit on Ethereum. For this reason, the Temperature updates every 48 blocks on Arbitrum during the Morning Auction.

#### View Functions

Getting user balances requires either (1) thousands of calls to RPC providers each time the Beanstalk UI loads (which often gets rate limited), or (2) reliance on infrastructure like the subgraphs. The migration to a L2 makes it possible to update the storage architecture to support getter functions for user balances.

#### App Storage Redesign

The difficulty of organizing storage data in Ethereum Diamonds can lead to security risks. From a development perspective, the L2 Migration is a great opportunity to address this issue.

#### Constants to State

Changing facets comes with additional security risks and migrating constants to state eliminates these risks. The migration to a L2 makes this migration more feasible.

### L2 Migration to Arbitrum

Removing the SiloFacet and waiting for all Deposits to Germinate (assuming there are Germinating Deposits at the time of BIP passage) before executing the migration simplifies the migration of Deposits.

Given that Beanstalk does not have control of Beans outside of the Beanstalk contract,  Circulating Beans at the time of the Reseed need to be manually migrated from L1 to L2.

Beanstalk assets in smart contract accounts cannot be automatically migrated given that Beanstalk cannot assume an owner of a smart contract is able to gain ownership of the same address on L2.

The diamond cut initialization scripts that migrate Beanstalk state are generalized to be executed across multiple transactions given that migration process will exceed the gas limit.

The remaining facets on L1 allow the continued support of Farm Balances on L1 Beanstalk. The L1TokenFacet must be updated to disallow transferring Beanstalk-related assets (which are migrated to L2) out of L1 Beanstalk.

#### New Whitelist

Deploying the Wells with an Upgradable Well Implementation (owned by Beanstalk) allows the BCM to fix bugs in the corresponding Wells without requiring a Silo migration from the vulnerable Well to a new one.

## Economic Rationale

### Secure Beanstalk

Ensuring that certain invariants are not violated as a result of transaction execution substantially increases the security of Beanstalk and has the potential to save the DAO a significant number of Beans from being spent on bug reports.

### Generalized Convert

Supporting LP → LP Converts significantly increases the efficacy of the Seed Gauge System with respect to achieving the optimal distribution of Bean liquidity.

Using the capped reserves in Multi Flow to measure Convert capacity allows Beanstalk to use the least stale data possible while preserving manipulation resistance.

Updating the existing `convert` function is necessary to penalize Farmers for Converting in a direction that causes the cumulative deltaB to move away from peg.

### Generalized Flood

Generalizing Flood the occur in multiple Wells at once is an essential peg maintenance tool for Beanstalk.

Capping the number of Pods that become Harvestable upon Flood to 0.1% of the Bean supply is a useful security measure given that it does not seem necessary to make all Pods Harvestable.

### Tractor

Decreasing friction for Farmers to automate the execution of their transactions improves the efficiency of Beanstalk.

### QoL Improvements

#### Whitelist Upgrades

Generalizing the whitelisting process substantially reduces the amount of code needed to be written to whitelist new assets, change oracles, change Gauge Point functions, etc. 

#### Morning Auction Adjustment

Decreasing the rate at which the Temperature increases during the Morning Auction should decrease the number of Pods issued by Beanstalk during times of high demand for Soil.

#### New Default Gauge Point Function

A Gauge Point function that factors in the distance between the current LP BDV distribution and the optimal may be more effective at issuing the proper amount of Grown Stalk to LP Depositors.

#### Minimum Bean to Max LP Ratio Upon Rain

Changing the Bean to Max LP Ratio to its minimum value before a Flood reflects that Beanstalk would prefer that Farmers first Convert down to add liquidity first before it mints and sells Beans directly on the market.

### L2 Migration to Arbitrum

Arbitrum is the largest Ethereum L2 by market cap and TVL. Migrating to a L2 substantially decreases the costs of interacting with Beanstalk.

BEAN:3CRV is migrated to BEAN:USDC given that there is no 3CRV on Arbitrum.

#### New Whitelist

A wider variety of options for Bean liquidity providers to receive Stalk and Seed rewards increases utility for Silo Depositors.

Whitelisting BEAN:USDC and BEAN:USDT gives Depositors the opportunity to earn Stalk and Seeds while being exposed to less volatile assets. The relatively low optimal LP BDV distribution of 12% for both BEAN:USDC and BEAN:USDT reflects the centralization risks of the two stablecoins. Using the Stable 2 Well Function in these Wells allows for better execution prices for traders around peg.

Setting the initial Gauge Points to 0 (the minimum value) for BEANwstETH and 1000 (the maximum value) for all other whitelisted assets reflects that nearly all Bean liquidity is already in the BEANwstETH Well due to the Unripe liquidity. Given this, Beanstalk should immediately start incentivizing holding the other whitelisted LP tokens.

Whitelisting BEAN:WBTC gives Depositors the opportunity to earn Stalk and Seeds while holding BTC exposure. The relatively low optimal LP BDV distribution of 20%  reflects the centralization risks of WBTC.

weETH has the largest TVL of any liquid staking derivative on Arbitrum. Basin does not have a Well Implementation that tracks rebasing tokens—thus, Basin (and thus, Beanstalk) must use weETH (i.e., the wrapped, non-rebasing version of eETH).

## Contract Changes

### L1 Initialization Contract

The `init` function on the ReseedL2Migration contract is called: [`0xE35c0397dBB43EB7E2cb28a182d857a3A42eFaDB`](https://etherscan.io/address/0xE35c0397dBB43EB7E2cb28a182d857a3A42eFaDB#code)

### L1 Beanstalk

All remaining facets and functions on L1 Beanstalk can be found in the Appendix section.

### L2 Beanstalk

The following facets are removed from Beanstalk (i.e., not added to L2 Beanstalk):
* `FundraiserFacet`
* `LegacyClaimWithdrawalFacet`
* `CurveFacet`
* `MigrationFacet`

All facets and functions added to L2 Beanstalk can be found in the Appendix section.

## Beans Minted

During the L2 migration, Beans are minted on Arbitrum corresponding to the amount in L1 Beanstalk (i.e., Beans underlying Bean Deposits, Beans underlying Deposited LP tokens, Ripe Beans, Farm Balances and Beans in Pod Orders).

After the conclusion of the L2 migration, anytime a Farmer migrates L1 Circulating Beans, L2 Beans will be minted. Similarly, any time a smart contract account owner on L1 migrates their assets, L2 Beans may be minted depending on which assets are migrated.

## Audit

The commit hash of this BIP is [faa0ec60a455b0afdd20ad86f28f41cbc52c2e2d](https://github.com/BeanstalkFarms/Beanstalk/pull/909/commits/faa0ec60a455b0afdd20ad86f28f41cbc52c2e2d).

An audit competition of this upgrade was held via Codehawks using commit hash [4e0ad0b964f74a1b4880114f4dd5b339bc69cd3e](https://github.com/Cyfrin/2024-05-beanstalk-the-finale/tree/4e0ad0b964f74a1b4880114f4dd5b339bc69cd3e). The final report can be read [here](https://codehawks.cyfrin.io/c/2024-05-beanstalk-the-finale/results?lt=contest&page=1&sc=reward&sj=reward&t=report).

Audit remediations were committed and documented in PRs [#970](https://github.com/BeanstalkFarms/Beanstalk/pull/970), [#1046](https://github.com/BeanstalkFarms/Beanstalk/pull/1046), [#1047](https://github.com/BeanstalkFarms/Beanstalk/pull/1047), [#1054](https://github.com/BeanstalkFarms/Beanstalk/pull/1054), [#1056](https://github.com/BeanstalkFarms/Beanstalk/pull/1056) and [#1076](https://github.com/BeanstalkFarms/Beanstalk/pull/1076). All changes were reviewed by 2 top Codehawks auditors.

The Upgradable Well Implementation and Stable 2 Well Function were audited by Code4rena—final report [here](https://code4rena.com/reports/2024-07-basin). The final report for the fix review is [here](https://code4rena.com/reports/2024-08-basin).

### Post Audit Changelog

#### Beanstalk

* [ca970be820d5aa13071ed032eb39f3d4aadcc43b](https://github.com/BeanstalkFarms/Beanstalk/commit/ca970be820d5aa13071ed032eb39f3d4aadcc43b), which divides by `1e6` in `LibGauge.issueGrownStalkPerBdv`;
* [d5714c86e949c58939f273c3edb5095c22877079](https://github.com/BeanstalkFarms/Beanstalk/commit/d5714c86e949c58939f273c3edb5095c22877079), which adds various try-catches in `LibPipelineConvert` for Wells that do not have any reserves;
* [0e5a0601bf3db45c670b700f1d20e6770ff8155a](https://github.com/BeanstalkFarms/Beanstalk/commit/0e5a0601bf3db45c670b700f1d20e6770ff8155a), which adds an additional check in `LibEvaluate.calcLpToSupplyRatio` that skips the evaluation of liquidity when the reserves are 0;
* [2d078411e35ed0c75f085633fa6883bce70e2d4f](https://github.com/BeanstalkFarms/Beanstalk/commit/2d078411e35ed0c75f085633fa6883bce70e2d4f), which updates `ReseedGlobal` to emit the `ShipmentRouteSet` event; an
* [b712b15bd82130a6595edad99fecb0f24c115fae](https://github.com/BeanstalkFarms/Beanstalk/commit/b712b15bd82130a6595edad99fecb0f24c115fae), which adds a try-catch in `LibWell.calculateTokenBeanPriceFromReserves` to fail gracefully. 

#### Basin

All of the following are implemented in [PR #146](https://github.com/BeanstalkFarms/Basin/pull/146):

* Update `MultiFlowPump` to fail gracefully when `calcRate` fails;
* Update `IMultiFlowWellFunction` to implement `ratioPrecision(uint256 j, bytes data)`, which returns the precision that `MultiFlowPump` should use when calculating the capped reserves;
* Update `MultiFlowPump` to use `ratioPrecision` when calculating `ratios[j]` in `calcReservesAtRatioSwap`;
* Update `ConstantProduct2`, `ConstantProduct`, and `Stable2` to implement `ratioPrecision`; and
* Update `IWellFunction` to implement `version`, allowing users to verify Well Functions.

## Effective

After either:

* A two-thirds supermajority is reached; or
* The Voting Period ends and either (1) 50% of Stalk is voting For or (2) one-third of Stalk, plus the amount of Stalk voting Against, is voting For,

The L1 BCM will:

* Execute a diamond cut that removes the SiloFacet and wait for 2 Seasons to pass in order for all Deposits to Germinate (only if there are Germinating Deposits at the time of BIP passage);
* Execute the BIP-50 diamond cut, which:
    * Calls the `ReseedL2Migration` initialization contract, which:
        * Pauses L1 Beanstalk; and
        * Transfers all BEANETH, BEANwstETH and BEAN3CRV in L1 Beanstalk to the L1 BCM;
    * Removes all facets except for `DiamondCutFacet`, `DiamondLoupeFacet`, `OwnershipFacet` and `PauseFacet`; and
    * Adds the `L1TokenFacet` and `L2MigrationFacet`;
* Remove corresponding liquidity from the BEAN:WETH, BEAN:wstETH and BEAN:3CRV pools;
* Swap all 3CRV for USDC; and
* Send all WETH, wstETH and USDC to Arbitrum using the Arbitrum-native bridge.

In addition, a deployer will:
* Fetch the state of L1 Beanstalk at the end of the block in which the BIP-50 diamond cut is executed;
* Deploy the L2 Beanstalk diamond on Arbitrum (in a Paused state);
* Execute the series of scripts to: 
    * Initialize the state of L2 Beanstalk;
    * Deploy all whitelisted Wells;
    * Mint Beans to be added as liquidity to the L2 BCM;
    * Mint remaining Beans (for Bean Deposits, Ripe Beans, Farm Balances and Beans in Pod Orders) to L2 Beanstalk; and
    * Mint Unripe Beans and Unripe LP to L2 Beanstalk; and
* Transfer ownership of L2 Beanstalk to the L2 BCM.

After which the L2 BCM will:
* Execute a series of scripts that verify the state of L2 Beanstalk;
* Claim ownership of L2 Beanstalk;
* Add liquidity to the BEAN:WETH, BEAN:wstETH and BEAN:USDC Wells;
* Transfer the corresponding BEANWETH, BEANwstETH and BEANUSDC Well LP tokens to L2 Beanstalk;
* Add all the facets mentioned in the Contract Changes section; and
* Unpause L2 Beanstalk.

## Appendix

### L1 Beanstalk

The following facets show all functions remaining on L1 Beanstalk:

#### L1 DiamondCutFacet

Remaining `DiamondCutFacet` address: [`0xDFeFF7592915bea8D040499E961E332BD453C249`](https://etherscan.io/address/0xDFeFF7592915bea8D040499E961E332BD453C249#code)

| Name                                   | Selector     | Action  | Type  | New Functionality |
|:---------------------------------------|:-------------|:--------|:------|:------------------|
| `diamondCut`                           | `0x1f931c1c` | None    | Write |                   |

#### L1 DiamondLoupeFacet

Remaining `DiamondLoupeFacet` address: [`0xB51D5C699B749E0382e257244610039dDB272Da0`](https://etherscan.io/address/0xB51D5C699B749E0382e257244610039dDB272Da0#code)

| Name                                   | Selector     | Action  | Type | New Functionality |
|:---------------------------------------|:-------------|:--------|:-----|:------------------|
| `facetAddress`                         | `0xcdffacc6` | None    | Read |                   |
| `facetAddresses`                       | `0x52ef6b2c` | None    | Read |                   |
| `facetFunctionSelectors`               | `0xadfca15e` | None    | Read |                   |
| `facets`                               | `0x7a0ed627` | None    | Read |                   |
| `supportsInterface`                    | `0x01ffc9a7` | None    | Read |                   |

#### L1 OwnershipFacet

Remaining `OwnershipFacet` address: [`0x5D45283Ff53aabDb93693095039b489Af8b18Cf7`](https://etherscan.io/address/0x5D45283Ff53aabDb93693095039b489Af8b18Cf7#code)

| Name                                   | Selector     | Action  | Type  | New Functionality |
|:---------------------------------------|:-------------|:--------|:------|:------------------|
| `claimOwnership`                       | `0x4e71e0c8` | None    | Write |                   |
| `owner`                                | `0x8da5cb5b` | None    | Read  |                   |
| `ownerCandidate`                       | `0x5f504a82` | None    | Read  |                   |
| `transferOwnership`                    | `0xf2fde38b` | None    | Write |                   |

#### L1 PauseFacet

Remaining `PauseFacet` address: [`0xeab4398f62194948cB25F45fEE4C46Fae2e91229`](https://etherscan.io/address/0xeab4398f62194948cB25F45fEE4C46Fae2e91229#code)

| Name                                   | Selector     | Action  | Type  | New Functionality |
|:---------------------------------------|:-------------|:--------|:------|:------------------|
| `pause`                                | `0x8456cb59` | None    | Write |                   |
| `unpause`                              | `0x3f4ba83a` | None    | Write |                   |

#### L1 L1TokenFacet

Added `L1TokenFacet` address: [`0x8F66044a9C95FaE9d38B8Bc30665eE04A2456501`](https://etherscan.io/address/0x8F66044a9C95FaE9d38B8Bc30665eE04A2456501#code)

| Name                                   | Selector     | Action  | Type  | New Functionality |
|:---------------------------------------|:-------------|:--------|:------|:------------------|
| `approveToken`                         | `0xda3e3397` | Add     | Write | ✓                 |
| `decreaseTokenAllowance`               | `0x0bc33ce4` | Add     | Write | ✓                 |
| `getAllBalance`                        | `0xfdb28811` | Add     | Read  |                   |
| `getAllBalances`                       | `0xb6fc38f9` | Add     | Read  |                   |
| `getBalance`                           | `0xd4fac45d` | Add     | Read  |                   |
| `getBalances`                          | `0x6a385ae9` | Add     | Read  |                   |
| `getExternalBalance`                   | `0x4667fa3d` | Add     | Read  |                   |
| `getExternalBalances`                  | `0xc3714723` | Add     | Read  |                   |
| `getInternalBalance`                   | `0x8a65d2e0` | Add     | Read  |                   |
| `getInternalBalances`                  | `0xa98edb17` | Add     | Read  |                   |
| `increaseTokenAllowance`               | `0xb39062e6` | Add     | Write | ✓                 |
| `onERC1155BatchReceived`               | `0xbc197c81` | Add     | Read  |                   |
| `onERC1155Received`                    | `0xf23a6e61` | Add     | Read  |                   |
| `permitToken`                          | `0x7c516e94` | Add     | Write | ✓                 |
| `tokenAllowance`                       | `0x8e8758d8` | Add     | Read  |                   |
| `tokenPermitDomainSeparator`           | `0x1f351f6a` | Add     | Read  |                   |
| `tokenPermitNonces`                    | `0x4edcab2d` | Add     | Read  |                   |
| `transferInternalTokenFrom`            | `0xd3f4ec6f` | Add     | Write | ✓                 |
| `transferToken`                        | `0x6204aa43` | Add     | Write | ✓                 |
| `unwrapEth`                            | `0xbd32fac3` | Add     | Write | ✓                 |
| `wrapEth`                              | `0x1c059365` | Add     | Write | ✓                 |

#### L1 L2MigrationFacet

Added `L2MigrationFacet` address: [`0xb7EA01231e518cd22E118165b290f5CC3263F5bB`](https://etherscan.io/address/0xb7EA01231e518cd22E118165b290f5CC3263F5bB#code)

| Name                                   | Selector     | Action  | Type  | New Functionality |
|:---------------------------------------|:-------------|:--------|:------|:------------------|
| `migrateL2Beans`                       | `0xfe5b810f` | Add     | Write | ✓                 |
| `approveL2Receiver`                    | `0xcb3fcc04` | Add     | Write | ✓                 |

### L2 Beanstalk

The following facets show all functions added to L2 Beanstalk. 

Notes: 
* All functions that were in L1 Beanstalk but are not added to L2 Beanstalk are indicated by a "Remove" in the Action column; and
* Functions that have changed solely as a result of at least one of the following do not have a "✓" in the New Functionality column:
    * Secure Beanstalk modifiers;
    * Changes to msg.sender as a result of introducing Tractor; or
    * Storage changes.

#### L2 DiamondCutFacet

Added `DiamondCutFacet` address: [`0x7B08f0efC94626D373c99fd39353217a371F90c0`](https://arbiscan.io/address/0x7B08f0efC94626D373c99fd39353217a371F90c0#code)

| Name                                   | Selector     | Action  | Type  | New Functionality |
|:---------------------------------------|:-------------|:--------|:------|:------------------|
| `diamondCut`                           | `0x1f931c1c` | Add     | Write |                   |

#### L2 DiamondLoupeFacet

Added `DiamondLoupeFacet` address: [`0x82aF8BC912284F916dBe7208074F3B16ea27BB40`](https://arbiscan.io/address/0x82aF8BC912284F916dBe7208074F3B16ea27BB40#code)

| Name                                   | Selector     | Action  | Type | New Functionality |
|:---------------------------------------|:-------------|:--------|:-----|:------------------|
| `facetAddress`                         | `0xcdffacc6` | Add     | Read |                   |
| `facetAddresses`                       | `0x52ef6b2c` | Add     | Read |                   |
| `facetFunctionSelectors`               | `0xadfca15e` | Add     | Read |                   |
| `facets`                               | `0x7a0ed627` | Add     | Read |                   |
| `supportsInterface`                    | `0x01ffc9a7` | Add     | Read |                   |

#### L2 OwnershipFacet

Added `OwnershipFacet` address: [`0x2cb2D140c42b79F602535e2447E7Afa980034464`](https://arbiscan.io/address/0x2cb2D140c42b79F602535e2447E7Afa980034464#code)

| Name                                   | Selector     | Action  | Type  | New Functionality |
|:---------------------------------------|:-------------|:--------|:------|:------------------|
| `claimOwnership`                       | `0x4e71e0c8` | Add     | Write |                   |
| `owner`                                | `0x8da5cb5b` | Add     | Read  |                   |
| `ownerCandidate`                       | `0x5f504a82` | Add     | Read  |                   |
| `transferOwnership`                    | `0xf2fde38b` | Add     | Write |                   |

#### L2 WhitelistFacet

Added `WhitelistFacet` address: [`0x7eF1D0449dD48189AF968586b2F91c8294ADDC07`](https://arbiscan.io/address/0x7eF1D0449dD48189AF968586b2F91c8294ADDC07#code)

| Name                                          | Selector     | Action    | Type  | New Functionality |
|:----------------------------------------------|:-------------|:----------|:------|:------------------|
| `whitelistTokenWithEncodeType`                | `N/A`        | Remove    | Write |                   |
| `whitelistTokenWithExternalImplementation`    | `N/A`        | Remove    | Write |                   |
| `getGaugePointImplementationForToken`         | `0x738cb80b` | Add (New) | Read  |  ✓                |
| `getLiquidityWeightImplementationForToken`    | `0x8104d586` | Add (New) | Read  |  ✓                |
| `getOracleImplementationForToken`             | `0x56b7b49c` | Add (New) | Read  |  ✓                |
| `updateGaugePointImplementationForToken`      | `0x4fb907a0` | Add (New) | Write |  ✓                |
| `updateLiquidityWeightImplementationForToken` | `0x88db1a6f` | Add (New) | Write |  ✓                |
| `updateOracleImplementationForToken`          | `0x874b87b9` | Add (New) | Write |  ✓                |
| `updateSeedGaugeSettings`                     | `0xa160f06a` | Add (New) | Write |  ✓                |
| `dewhitelistToken`                            | `0x86b40a1b` | Add       | Write |                   |
| `getSiloTokens`                               | `0xe9522c08` | Add       | Read  |                   |
| `getWhitelistStatus`                          | `0xd9ba32fc` | Add       | Read  |                   |
| `getWhitelistStatuses`                        | `0x170cf084` | Add       | Read  |                   |
| `getWhitelistedLpTokens`                      | `0x9d1d2877` | Add       | Read  |                   |
| `getWhitelistedTokens`                        | `0xe26f7900` | Add       | Read  |                   |
| `getWhitelistedWellLpTokens`                  | `0x76a7bc84` | Add       | Read  |                   |
| `updateGaugeForToken`                         | `0x19b8f518` | Add       | Write |  ✓                |
| `updateStalkPerBdvPerSeasonForToken`          | `0xf18d9ed0` | Add       | Write |                   |
| `whitelistToken`                              | `0x4bfd2d75` | Add       | Write |  ✓                |

#### L2 UnripeFacet

Added `UnripeFacet` address: [`0x0b980ab39F9fDf3226b98Bc32d96EC180fd61687`](https://arbiscan.io/address/0x0b980ab39F9fDf3226b98Bc32d96EC180fd61687#code)

| Name                                   | Selector     | Action  | Type  | New Functionality |
|:---------------------------------------|:-------------|:--------|:------|:------------------|
| `pick`                                 | `N/A`        | Remove  | Write |                   |
| `picked`                               | `N/A`        | Remove  | Read  |                   |
| `addMigratedUnderlying`                | `0x787cee99` | Add     | Write |                   |
| `addUnripeToken`                       | `0xfa345569` | Add     | Write |                   |
| `balanceOfPenalizedUnderlying`         | `0x1acc0a47` | Add     | Read  |                   |
| `balanceOfUnderlying`                  | `0x1be655e8` | Add     | Read  |                   |
| `chop`                                 | `0x9a516cad` | Add     | Write |                   |
| `getLockedBeans`                       | `0x087d78b4` | Add     | Read  |                   |
| `getLockedBeansFromTwaReserves`        | `0x7caa025f` | Add     | Read  |                   |
| `getLockedBeansUnderlyingUnripeBean`   | `0xbfe2f3be` | Add     | Read  |                   |
| `getLockedBeansUnderlyingUnripeLP`     | `0x33f37f27` | Add     | Read  |                   |
| `getPenalizedUnderlying`               | `0x6de45df2` | Add     | Read  |                   |
| `getPenalty`                           | `0x014a8a49` | Add     | Read  |                   |
| `getPercentPenalty`                    | `0xbb7de478` | Add     | Read  |                   |
| `getRecapFundedPercent`                | `0x43cc4ee0` | Add     | Read  |                   |
| `getRecapPaidPercent`                  | `0xab434eb7` | Add     | Read  |                   |
| `getRecapitalized`                     | `0xe68a543a` | Add     | Read  |                   |
| `getTotalUnderlying`                   | `0xadef4533` | Add     | Read  |                   |
| `getUnderlying`                        | `0x9f06b3fa` | Add     | Read  |                   |
| `getUnderlyingPerUnripeToken`          | `0xb8a04d1b` | Add     | Read  |                   |
| `getUnderlyingToken`                   | `0x691bcc88` | Add     | Read  |                   |
| `isUnripe`                             | `0xfc6a19df` | Add     | Read  |                   |
| `switchUnderlyingToken`                | `0xa33fa99f` | Add     | Write |                   |

#### L2 MetadataFacet

Added `MetadataFacet` address: [`0x958679Ab3CC0961F4339FaeCcbf36a1d5906cbF5`](https://arbiscan.io/address/0x958679Ab3CC0961F4339FaeCcbf36a1d5906cbF5#code)

| Name                                   | Selector     | Action  | Type  | New Functionality |
|:---------------------------------------|:-------------|:--------|:------|:------------------|
| `imageURI`                             | `0xc20b8071` | Add     | Read  |                   |
| `name`                                 | `0x06fdde03` | Add     | Read  |                   |
| `symbol`                               | `0x95d89b41` | Add     | Read  |                   |
| `uri`                                  | `0x0e89341c` | Add     | Read  |                   |

#### L2 SeasonFacet

Added `SeasonFacet` address: [`0x552322CD960FFB809d91012CE05d6FBB86BaE290`](https://arbiscan.io/address/0x552322CD960FFB809d91012CE05d6FBB86BaE290#code)

| Name                                   | Selector     | Action    | Type  | New Functionality |
|:---------------------------------------|:-------------|:----------|:------|:------------------|
| `getShipmentRoutes`                    | `0xfd497a68` | Add (New) | Write |  ✓                |
| `setShipmentRoutes`                    | `0xf1e2dfb0` | Add (New) | Read  |  ✓                |
| `gm`                                   | `0x64ee4b80` | Add       | Write |                   |
| `seasonTime`                           | `0xca7b7d7b` | Add       | Read  |                   |
| `sunrise`                              | `0xfc06d2a6` | Add       | Write |                   |

#### L2 SeasonGettersFacet

Added `SeasonGettersFacet` address: [`0xdf522AC66735CB506D15236CF35938588f29e34B`](https://arbiscan.io/address/0xdf522AC66735CB506D15236CF35938588f29e34B#code)

| Name                                     | Selector     | Action    | Type  | New Functionality |
|:-----------------------------------------|:-------------|:----------|:------|:------------------|
| `getBeanEthGaugePointsPerBdv`            | `N/A`        | Remove    | Read  |                   |
| `getSopWell`                             | `N/A`        | Remove    | Read  |                   |
| `cumulativeCurrentDeltaB`                | `0x89a218d2` | Add (New) | Read  |  ✓                |
| `getAbsBeanToMaxLpRatioChangeFromCaseId` | `0xe53b479e` | Add (New) | Read  |  ✓                |
| `getAbsTemperatureChangeFromCaseId`      | `0x3cee5dea` | Add (New) | Read  |  ✓                |
| `getCaseData`                            | `0x8097f0ca` | Add (New) | Read  |  ✓                |
| `getCases`                               | `0x065cb594` | Add (New) | Read  |  ✓                |
| `getChangeFromCaseId`                    | `0x43e0156a` | Add (New) | Read  |  ✓                |
| `getDeltaPodDemandLowerBound`            | `0x57801d87` | Add (New) | Read  |  ✓                |
| `getDeltaPodDemandUpperBound`            | `0x70fd1b06` | Add (New) | Read  |  ✓                |
| `getEvaluationParameters`                | `0xda61af62` | Add (New) | Read  |  ✓                |
| `getExcessivePriceThreshold`             | `0x44fb7cc3` | Add (New) | Read  |  ✓                |
| `getLpToSupplyRatioUpperBound`           | `0x1eedbfbb` | Add (New) | Read  |  ✓                |
| `getLpToSupplyRatioOptimal`              | `0x1f48a553` | Add (New) | Read  |  ✓                |
| `getLpToSupplyRatioLowerBound`           | `0x11a8d895` | Add (New) | Read  |  ✓                |
| `getMaxBeanMaxLpGpPerBdvRatio`           | `0xab843b34` | Add (New) | Read  |  ✓                |
| `getMinBeanMaxLpGpPerBdvRatio`           | `0xb3c39ce5` | Add (New) | Read  |  ✓                |
| `getPodRateLowerBound`                   | `0xfd6d1483` | Add (New) | Read  |  ✓                |
| `getPodRateOptimal`                      | `0xdd9330d2` | Add (New) | Read  |  ✓                |
| `getPodRateUpperBound`                   | `0x08fa96d3` | Add (New) | Read  |  ✓                |
| `getRelBeanToMaxLpRatioChangeFromCaseId` | `0x35870a7a` | Add (New) | Read  |  ✓                |
| `getRelTemperatureChangeFromCaseId`      | `0x4d65f762` | Add (New) | Read  |  ✓                |
| `getSeasonStruct`                        | `0x738ad142` | Add (New) | Read  |  ✓                |
| `getSeasonTimestamp`                     | `0xf07f0760` | Add (New) | Read  |  ✓                |
| `getTargetSeasonsToCatchUp`              | `0xcb677411` | Add (New) | Read  |  ✓                |
| `getWellsByDeltaB`                       | `0xbf170533` | Add (New) | Read  |  ✓                |
| `poolCurrentDeltaB`                      | `0x8223eac8` | Add (New) | Read  |  ✓                |
| `abovePeg`                               | `0x2a27c499` | Add       | Read  |                   |
| `getLargestLiqWell`                      | `0xd1943f7f` | Add       | Read  |                   |
| `getTotalUsdLiquidity`                   | `0xbbf459a7` | Add       | Read  |                   |
| `getTotalWeightedUsdLiquidity`           | `0xf788b47c` | Add       | Read  |                   |
| `getTwaLiquidityForWell`                 | `0xa13a3742` | Add       | Read  |                   |
| `getWeightedTwaLiquidityForWell`         | `0x93c9e531` | Add       | Read  |                   |
| `paused`                                 | `0x5c975abb` | Add       | Read  |                   |
| `plentyPerRoot`                          | `0x3fccd20c` | Add       | Read  |                   |
| `poolDeltaB`                             | `0x471bcdbe` | Add       | Read  |                   |
| `rain`                                   | `0x43def26e` | Add       | Read  |                   |
| `season`                                 | `0xc50b0fb0` | Add       | Read  |                   |
| `sunriseBlock`                           | `0x3b2ecb70` | Add       | Read  |                   |
| `time`                                   | `0x16ada547` | Add       | Read  |                   |
| `totalDeltaB`                            | `0x06c499d8` | Add       | Read  |                   |
| `weather`                                | `0x686b6159` | Add       | Read  |                   |
| `wellOracleSnapshot`                     | `0x597490c0` | Add       | Read  |                   |

#### L2 GaugeGettersFacet

Added `GaugeGettersFacet` address: [`0x16b6B2Deb4b19DDb664167CF8cBE601DFA9a87e5`](https://arbiscan.io/address/0x16b6B2Deb4b19DDb664167CF8cBE601DFA9a87e5#code)

| Name                                   | Selector     | Action    | Type  | New Functionality |
|:---------------------------------------|:-------------|:----------|:------|:------------------|
| `getLargestGpPerBDV`                   | `0x6bbf3305` | Add (New) | Read  |  ✓                |
| `calcGaugePointsWithParams`            | `0x7046c9a6` | Add       | Read  |                   |
| `getAverageGrownStalkPerBdv`           | `0x7ba6cbf8` | Add       | Read  |                   |
| `getAverageGrownStalkPerBdvPerSeason`  | `0xeb0e1215` | Add       | Read  |                   |
| `getBeanGaugePointsPerBdv`             | `0x69aa7e02` | Add       | Read  |                   |
| `getBeanToMaxLpGpPerBdvRatio`          | `0xcc88d4f9` | Add       | Read  |                   |
| `getBeanToMaxLpGpPerBdvRatioScaled`    | `0x673c75f0` | Add       | Read  |                   |
| `getDeltaPodDemand`                    | `0x64b3496b` | Add       | Read  |                   |
| `getGaugePoints`                       | `0x93523425` | Add       | Read  |                   |
| `getGaugePointsPerBdvForToken`         | `0x64887852` | Add       | Read  |                   |
| `getGaugePointsPerBdvForWell`          | `0xb2b0556d` | Add       | Read  |                   |
| `getGaugePointsWithParams`             | `0x141933bf` | Add       | Read  |                   |
| `getGrownStalkIssuedPerGp`             | `0xf98da2de` | Add       | Read  |                   |
| `getGrownStalkIssuedPerSeason`         | `0x383f170f` | Add       | Read  |                   |
| `getLiquidityToSupplyRatio`            | `0xcb2d0a3c` | Add       | Read  |                   |
| `getPodRate`                           | `0x11242145` | Add       | Read  |                   |
| `getSeedGauge`                         | `0x6af8e5a4` | Add       | Read  |                   |
| `getTotalBdv`                          | `0x50539159` | Add       | Read  |                   |

#### L2 PauseFacet

Added `PauseFacet` address: [`0x7ee24734b97902E6081D702514776416F11F971b`](https://arbiscan.io/address/0x7ee24734b97902E6081D702514776416F11F971b#code)

| Name                                   | Selector     | Action  | Type  | New Functionality |
|:---------------------------------------|:-------------|:--------|:------|:------------------|
| `pause`                                | `0x8456cb59` | Add     | Write |                   |
| `unpause`                              | `0x3f4ba83a` | Add     | Write |                   |

#### L2 MarketplaceFacet

Added `MarketplaceFacet` address: [`0x6464446d74C27961396A126b2d449aBdDea354cd`](https://arbiscan.io/address/0x6464446d74C27961396A126b2d449aBdDea354cd#code)

| Name                                   | Selector     | Action    | Type  | New Functionality |
|:---------------------------------------|:-------------|:----------|:------|:------------------|
| `cancelPodOrderV2`                     | `N/A`        | Remove    | Write |                   |
| `createPodListingV2`                   | `N/A`        | Remove    | Write |                   |
| `createPodOrderV2`                     | `N/A`        | Remove    | Write |                   |
| `fillPodListingV2`                     | `N/A`        | Remove    | Write |                   |
| `fillPodOrderV2`                       | `N/A`        | Remove    | Write |                   |
| `getAmountBeansToFillOrderV2`          | `N/A`        | Remove    | Read  |                   |
| `getAmountPodsFromFillListingV2`       | `N/A`        | Remove    | Read  |                   |
| `podListing`                           | `N/A`        | Remove    | Read  |                   |
| `podOrder`                             | `N/A`        | Remove    | Read  |                   |
| `podOrderById`                         | `N/A`        | Remove    | Read  |                   |
| `podOrderV2`                           | `N/A`        | Remove    | Read  |                   |
| `getOrderId`                           | `0x631076dd` | Add (New) | Read  |  ✓                |
| `getPodListing`                        | `0x98c02432` | Add (New) | Read  |  ✓                |
| `getPodOrder`                          | `0x674a3e67` | Add (New) | Read  |  ✓                |
| `transferPlots`                        | `0x31ed3796` | Add (New) | Write |  ✓                |
| `allowancePods`                        | `0xb151226a` | Add       | Read  |                   |
| `approvePods`                          | `0x0711f012` | Add       | Write |                   |
| `cancelPodListing`                     | `0x8d398973` | Add       | Write |                   |
| `cancelPodOrder`                       | `0x9ed2801b` | Add       | Write |                   |
| `createPodListing`                     | `0x65865af6` | Add       | Write |                   |
| `createPodOrder`                       | `0x37b4d2ec` | Add       | Write |                   |
| `fillPodListing`                       | `0xf7f228a2` | Add       | Write |                   |
| `fillPodOrder`                         | `0xed8c792f` | Add       | Write |                   |
| `transferPlot`                         | `0xceb39673` | Add       | Write |                   |

#### L2 FieldFacet

Added `FieldFacet` address: [`0xE6f9cE8737fa856e2aEeD2925DB39Fcac25c6513`](https://arbiscan.io/address/0xE6f9cE8737fa856e2aEeD2925DB39Fcac25c6513#code)

| Name                                   | Selector     | Action    | Type  | New Functionality |
|:---------------------------------------|:-------------|:----------|:------|:------------------|
| `yield`                                | `N/A`        | Remove    | Read  |                   |
| `activeField`                          | `0xd1eba544` | Add (New) | Read  |  ✓                |
| `addField`                             | `0xb94e871c` | Add (New) | Write |  ✓                |
| `balanceOfPods`                        | `0x9a337c1d` | Add (New) | Read  |  ✓                |
| `fieldCount`                           | `0xbb485bbd` | Add (New) | Read  |  ✓                |
| `getPlotIndexesFromAccount`            | `0x253fcfb5` | Add (New) | Read  |  ✓                |
| `getPlotsFromAccount`                  | `0x91b24284` | Add (New) | Read  |  ✓                |
| `isHarvesting`                         | `0x4bea67c4` | Add (New) | Read  |  ✓                |
| `setActiveField`                       | `0x057c571b` | Add (New) | Write |  ✓                |
| `totalHarvestableForActiveField`       | `0x237dbac5` | Add (New) | Read  |  ✓                |
| `harvest`                              | `0xe9bbb033` | Add       | Write |  ✓                |
| `harvestableIndex`                     | `0xb511654d` | Add       | Read  |  ✓                |
| `maxTemperature`                       | `0x7907091f` | Add       | Read  |                   |
| `plot`                                 | `0x9ee7ea12` | Add       | Read  |                   |
| `podIndex`                             | `0xccda40b9` | Add       | Read  |  ✓                |
| `remainingPods`                        | `0x56ba3e24` | Add       | Read  |                   |
| `sow`                                  | `0x32ab68ce` | Add       | Write |                   |
| `sowWithMin`                           | `0x553030d0` | Add       | Write |                   |
| `temperature`                          | `0xadccea12` | Add       | Read  |                   |
| `totalHarvestable`                     | `0x2e76f597` | Add       | Read  |  ✓                |
| `totalHarvested`                       | `0x352525a6` | Add       | Read  |  ✓                |
| `totalPods`                            | `0xf1e0a211` | Add       | Read  |  ✓                |
| `totalSoil`                            | `0x3285008a` | Add       | Read  |                   |
| `totalUnharvestable`                   | `0xf29ffe94` | Add       | Read  |  ✓                |

#### L2 FertilizerFacet

Added `FertilizerFacet` address: [`0x6f252Ecf79aF1bd57c48047a8B109001FFB4c1DB`](https://arbiscan.io/address/0x6f252Ecf79aF1bd57c48047a8B109001FFB4c1DB#code)

| Name                         | Selector     | Action    | Type  | New Functionality |
|:-----------------------------|:-------------|:----------|:------|:------------------|
| `leftoverBeans`              | `0x8bb3aba8` | Add (New) | Read  |  ✓                |
| `rinsableSprouts`            | `0x5d7db3b6` | Add (New) | Read  |  ✓                |
| `rinsedSprouts`              | `0x7562d880` | Add (New) | Read  |  ✓                |
| `_getMintFertilizerOut`      | `0x94daa221` | Add       | Read  |                   |
| `balanceOfBatchFertilizer`   | `0x304ec65d` | Add       | Read  |                   |
| `balanceOfFertilized`        | `0xb6f42085` | Add       | Read  |                   |
| `balanceOfFertilizer`        | `0x1799b3b2` | Add       | Read  |                   |
| `balanceOfUnfertilized`      | `0x1edb6be1` | Add       | Read  |                   |
| `beansPerFertilizer`         | `0x9bb4e35a` | Add       | Read  |                   |
| `beginBarnRaiseMigration`    | `0xe3d4e44c` | Add       | Write |                   |
| `claimFertilized`            | `0x83e08888` | Add       | Write |                   |
| `getActiveFertilizer`        | `0xdc6ba285` | Add       | Read  |                   |
| `getBarnRaiseToken`          | `0xf255da60` | Add       | Read  |                   |
| `getBarnRaiseWell`           | `0x93a39bea` | Add       | Read  |                   |
| `getCurrentHumidity`         | `0x39448802` | Add       | Read  |                   |
| `getEndBpf`                  | `0xc85951a1` | Add       | Read  |                   |
| `getFertilizer`              | `0x9c45a1d5` | Add       | Read  |                   |
| `getFertilizers`             | `0x34af5416` | Add       | Read  |                   |
| `getFirst`                   | `0x1e223143` | Add       | Read  |                   |
| `getHumidity`                | `0x29130a66` | Add       | Read  |                   |
| `getLast`                    | `0x4d622831` | Add       | Read  |                   |
| `getMintFertilizerOut`       | `0x69744dd0` | Add       | Read  |                   |
| `getNext`                    | `0xf4a057e2` | Add       | Read  |                   |
| `getTotalRecapDollarsNeeded` | `0x12cb5eab` | Add       | Read  |                   |
| `isFertilizing`              | `0x6ae1c014` | Add       | Read  |                   |
| `mintFertilizer`             | `0x363591d0` | Add       | Write |                   |
| `payFertilizer`              | `0xd47aee59` | Add       | Write |                   |
| `remainingRecapitalization`  | `0x4a16607c` | Add       | Read  |                   |
| `totalFertilizedBeans`       | `0x4f9a9678` | Add       | Read  |                   |
| `totalFertilizerBeans`       | `0xf9c4ebde` | Add       | Read  |                   |
| `totalUnfertilizedBeans`     | `0xa3ef48c9` | Add       | Read  |                   |

#### L2 FarmFacet

Added `FarmFacet` address: [`0xd4A0797D7700bbA801d2DeD34e5d44480D0061Fe`](https://arbiscan.io/address/0xd4A0797D7700bbA801d2DeD34e5d44480D0061Fe#code)

| Name                         | Selector     | Action  | Type  | New Functionality |
|:-----------------------------|:-------------|:--------|:------|:------------------|
| `advancedFarm`               | `0x36bfafbd` | Add     | Write |                   |
| `farm`                       | `0x300dd6cf` | Add     | Write |                   |

#### L2 SiloFacet

Added `SiloFacet` address: [`0xA89Fbf550A453f0eD9D75DAac706fa41eE7F9A1d`](https://arbiscan.io/address/0xA89Fbf550A453f0eD9D75DAac706fa41eE7F9A1d#code)

| Name                    | Selector     | Action  | Type  | New Functionality |
|:------------------------|:-------------|:--------|:------|:------------------|
| `deposit`               | `0xf19ed6be` | Add     | Write |                   |
| `safeBatchTransferFrom` | `0x2eb2c2d6` | Add     | Write |                   |
| `safeTransferFrom`      | `0xf242432a` | Add     | Write |                   |
| `transferDeposit`       | `0x081d77ba` | Add     | Write |                   |
| `transferDeposits`      | `0xc56411f6` | Add     | Write |                   |
| `withdrawDeposit`       | `0xe348f82b` | Add     | Write |                   |
| `withdrawDeposits`      | `0x27e047f1` | Add     | Write |                   |

#### L2 SiloGettersFacet

Added `SiloGettersFacet` address: [`0x51757F6c0A662B4fB57E96a903b199d9D0fCd312`](https://arbiscan.io/address/0x51757F6c0A662B4fB57E96a903b199d9D0fCd312#code)

| Name                                           | Selector     | Action    | Type | New Functionality |
|:-----------------------------------------------|:-------------|:----------|:-----|:------------------|
| `getLegacySeedsPerToken`                       | `N/A`        | Remove    | Read |                   |
| `migrationNeeded`                              | `N/A`        | Remove    | Read |                   |
| `seasonToStem`                                 | `N/A`        | Remove    | Read |                   |
| `balanceOfGrownStalkMultiple`                  | `0xcb65f1b1` | Add (New) | Read |  ✓                |
| `balanceOfPlantableSeeds`                      | `0x80c9084b` | Add (New) | Read |  ✓                |
| `bdvs`                                         | `0xd31e4d66` | Add (New) | Read |  ✓                |
| `calculateStemForTokenFromGrownStalk`          | `0x0b6413b0` | Add (New) | Read |  ✓                |
| `getAddressAndStem`                            | `0x805a343f` | Add (New) | Read |  ✓                |
| `getBeanIndex`                                 | `0xb592d450` | Add (New) | Read |  ✓                |
| `getBeanstalkTokens`                           | `0x3c22a23c` | Add (New) | Read |  ✓                |
| `getDepositsForAccount` (no `token` parameter) | `0x823ccbe9` | Add (New) | Read |  ✓                |
| `getDepositsForAccount` (accepts `tokens[]`)   | `0xe73c165b` | Add (New) | Read |  ✓                |
| `getIndexForDepositId`                         | `0x55d0807e` | Add (New) | Read |  ✓                |
| `getMowStatus` (accepts `tokens[]`)            | `0x047c92cf` | Add (New) | Read |  ✓                |
| `getNonBeanTokenAndIndexFromWell`              | `0xf6225118` | Add (New) | Read |  ✓                |
| `getStemTips`                                  | `0x9cf67d70` | Add (New) | Read |  ✓                |
| `getTokenDepositIdsForAccount`                 | `0x54369b5b` | Add (New) | Read |  ✓                |
| `getTokenDepositsForAccount`                   | `0xe49b77f5` | Add (New) | Read |  ✓                |
| `getTotalSiloDeposited`                        | `0xb13d3024` | Add (New) | Read |  ✓                |
| `getTotalSiloDepositedBdv`                     | `0xbd637860` | Add (New) | Read |  ✓                |
| `stalkEarnedPerSeason`                         | `0xedd2d167` | Add (New) | Read |  ✓                |
| `balanceOf`                                    | `0x00fdd58e` | Add       | Read |                   |
| `balanceOfBatch`                               | `0x4e1273f4` | Add       | Read |                   |
| `balanceOfDepositedBDV`                        | `0xbc8514cf` | Add       | Read |                   |
| `balanceOfEarnedBeans`                         | `0x3e465a2e` | Add       | Read |                   |
| `balanceOfEarnedStalk`                         | `0x341b94d5` | Add       | Read |                   |
| `balanceOfFinishedGerminatingStalkAndRoots`    | `0xc063989e` | Add       | Read |                   |
| `balanceOfGerminatingStalk`                    | `0x838082b5` | Add       | Read |                   |
| `balanceOfGrownStalk`                          | `0x8915ba24` | Add       | Read |                   |
| `balanceOfPlenty`                              | `0xb02e7162` | Add       | Read |  ✓                |
| `balanceOfRainRoots`                           | `0x69fbad94` | Add       | Read |                   |
| `balanceOfRoots`                               | `0xba39dc02` | Add       | Read |                   |
| `balanceOfSop`                                 | `0xa7bf680f` | Add       | Read |  ✓                |
| `balanceOfStalk`                               | `0x8eeae310` | Add       | Read |                   |
| `balanceOfYoungAndMatureGerminatingStalk`      | `0x0fb01e05` | Add       | Read |                   |
| `bdv`                                          | `0x8c1e6f22` | Add       | Read |  ✓                |
| `getDeposit`                                   | `0x61449212` | Add       | Read |                   |
| `getDepositId`                                 | `0x98f2b8ad` | Add       | Read |                   |
| `getEvenGerminating`                           | `0x1ca5f625` | Add       | Read |                   |
| `getGerminatingRootsForSeason`                 | `0x96e7f21e` | Add       | Read |                   |
| `getGerminatingStalkAndRootsForSeason`         | `0x4118140a` | Add       | Read |                   |
| `getGerminatingStalkForSeason`                 | `0x9256dccd` | Add       | Read |                   |
| `getGerminatingStem`                           | `0xa953f06d` | Add       | Read |                   |
| `getGerminatingStems`                          | `0xe5b17f2a` | Add       | Read |                   |
| `getGerminatingTotalDeposited`                 | `0xc25a156c` | Add       | Read |                   |
| `getGerminatingTotalDepositedBdv`              | `0x9b3ec513` | Add       | Read |                   |
| `getLastMowedStem`                             | `0x7fc06e12` | Add       | Read |                   |
| `getMowStatus` (accepts `token`)               | `0xdc25a650` | Add       | Read |                   |
| `getOddGerminating`                            | `0x85167e51` | Add       | Read |                   |
| `getTotalDeposited`                            | `0x0c9c31bd` | Add       | Read |                   |
| `getTotalDepositedBDV`                         | `0x9d6a924e` | Add       | Read |                   |
| `getTotalGerminatingAmount`                    | `0xb45ef2eb` | Add       | Read |                   |
| `getTotalGerminatingBdv`                       | `0x9dcf67f0` | Add       | Read |                   |
| `getTotalGerminatingStalk`                     | `0x7d4a51cb` | Add       | Read |                   |
| `getYoungAndMatureGerminatingTotalStalk`       | `0x5a8e63e3` | Add       | Read |                   |
| `grownStalkForDeposit`                         | `0x3a1b0606` | Add       | Read |                   |
| `lastSeasonOfPlenty`                           | `0xbe6547d2` | Add       | Read |                   |
| `lastUpdate`                                   | `0xcb03fb1e` | Add       | Read |                   |
| `stemStartSeason`                              | `0xbc771977` | Add       | Read |                   |
| `stemTipForToken`                              | `0xabed2d41` | Add       | Read |                   |
| `tokenSettings`                                | `0xe923e8d4` | Add       | Read |                   |
| `totalEarnedBeans`                             | `0xfd9de166` | Add       | Read |                   |
| `totalRainRoots`                               | `0xaea72f96` | Add       | Read |                   |
| `totalRoots`                                   | `0x46544166` | Add       | Read |                   |
| `totalStalk`                                   | `0x7b52fadf` | Add       | Read |                   |

#### L2 EnrootFacet

Added `EnrootFacet` address: [`0xD9171D21C414AE676946a60cd226b3EDa5aC3a2A`](https://arbiscan.io/address/0xD9171D21C414AE676946a60cd226b3EDa5aC3a2A#code)

| Name                                         | Selector     | Action    | Type  | New Functionality |
|:---------------------------------------------|:-------------|:----------|:------|:------------------|
| `balanceOfRevitalizedStalk`                  | `0xe43b44ee` | Add (New) | Read  |  ✓                |
| `enrootDeposit`                              | `0x0b58f073` | Add       | Write |                   |
| `enrootDeposits`                             | `0x88fcd169` | Add       | Write |                   |

#### L2 TokenFacet

Added `TokenFacet` address: [`0x4D26Caf0778D651922e89c546f09Ae852cc4933a`](https://arbiscan.io/address/0x4D26Caf0778D651922e89c546f09Ae852cc4933a#code)

| Name                                   | Selector     | Action  | Type  | New Functionality |
|:---------------------------------------|:-------------|:--------|:------|:------------------|
| `approveToken`                         | `0xda3e3397` | Add     | Write |                   |
| `decreaseTokenAllowance`               | `0x0bc33ce4` | Add     | Write |                   |
| `getAllBalance`                        | `0xfdb28811` | Add     | Read  |                   |
| `getAllBalances`                       | `0xb6fc38f9` | Add     | Read  |                   |
| `getBalance`                           | `0xd4fac45d` | Add     | Read  |                   |
| `getBalances`                          | `0x6a385ae9` | Add     | Read  |                   |
| `getExternalBalance`                   | `0x4667fa3d` | Add     | Read  |                   |
| `getExternalBalances`                  | `0xc3714723` | Add     | Read  |                   |
| `getInternalBalance`                   | `0x8a65d2e0` | Add     | Read  |                   |
| `getInternalBalances`                  | `0xa98edb17` | Add     | Read  |                   |
| `increaseTokenAllowance`               | `0xb39062e6` | Add     | Write |                   |
| `onERC1155BatchReceived`               | `0xbc197c81` | Add     | Read  |                   |
| `onERC1155Received`                    | `0xf23a6e61` | Add     | Read  |                   |
| `permitToken`                          | `0x7c516e94` | Add     | Write |                   |
| `tokenAllowance`                       | `0x8e8758d8` | Add     | Read  |                   |
| `tokenPermitDomainSeparator`           | `0x1f351f6a` | Add     | Read  |                   |
| `tokenPermitNonces`                    | `0x4edcab2d` | Add     | Read  |                   |
| `transferInternalTokenFrom`            | `0xd3f4ec6f` | Add     | Write |                   |
| `transferToken`                        | `0x6204aa43` | Add     | Write |                   |
| `unwrapEth`                            | `0xbd32fac3` | Add     | Write |                   |
| `wrapEth`                              | `0x1c059365` | Add     | Write |                   |

#### L2 DepotFacet

Added `DepotFacet` address: [`0x47422eEEcd1cE855dcf59eE7EaEb23c6A4666699`](https://arbiscan.io/address/0x47422eEEcd1cE855dcf59eE7EaEb23c6A4666699#code)

| Name                                   | Selector     | Action  | Type  | New Functionality |
|:---------------------------------------|:-------------|:--------|:------|:------------------|
| `advancedPipe`                         | `0xb452c7ae` | Add     | Write |                   |
| `etherPipe`                            | `0x6e47d07b` | Add     | Write |                   |
| `multiPipe`                            | `0xcabec62b` | Add     | Write |                   |
| `pipe`                                 | `0x08e1a0ab` | Add     | Write |                   |
| `readPipe`                             | `0xdd756c4f` | Add     | Read  |                   |

#### L2 TokenSupportFacet

Added `TokenSupportFacet` address: [`0xcC0f8117B6c0c45C15D4d306Cdb14454263f33Ba`](https://arbiscan.io/address/0xcC0f8117B6c0c45C15D4d306Cdb14454263f33Ba#code)

| Name                                   | Selector     | Action  | Type  | New Functionality |
|:---------------------------------------|:-------------|:--------|:------|:------------------|
| `batchTransferERC1155`                 | `0xa9412a59` | Add     | Write |                   |
| `permitERC20`                          | `0xb442b398` | Add     | Write |                   |
| `permitERC721`                         | `0x4935ed43` | Add     | Write |                   |
| `transferERC1155`                      | `0x0a7e880c` | Add     | Write |                   |
| `transferERC721`                       | `0x1aca6376` | Add     | Write |                   |

#### L2 ConvertFacet

Added `ConvertFacet` address: [`0xd7a7Ec3B2EC70EdFFfB969f94436908fB53B3B85`](https://arbiscan.io/address/0xd7a7Ec3B2EC70EdFFfB969f94436908fB53B3B85#code)

| Name                                   | Selector     | Action  | Type  | New Functionality |
|:---------------------------------------|:-------------|:--------|:------|:------------------|
| `convert`                              | `0xb362a6e8` | Add     | Write |  ✓                |

#### L2 ConvertGettersFacet

Added `ConvertGettersFacet` address: [`0x3D5Cd5A7C7312bF005de78B09e125b34165a69Ec`](https://arbiscan.io/address/0x3D5Cd5A7C7312bF005de78B09e125b34165a69Ec#code)

| Name                                   | Selector     | Action    | Type  | New Functionality |
|:---------------------------------------|:-------------|:----------|:------|:------------------|
| `calculateDeltaBFromReserves`          | `0xd052f0d5` | Add (New) | Read  |   ✓               |
| `calculateStalkPenalty`                | `0xb325d2ef` | Add (New) | Read  |   ✓               |
| `cappedReservesDeltaB`                 | `0x6842f2b3` | Add (New) | Read  |   ✓               |
| `getOverallConvertCapacity`            | `0xf66d5589` | Add (New) | Read  |   ✓               |
| `getWellConvertCapacity`               | `0xb905065b` | Add (New) | Read  |   ✓               |
| `overallCappedDeltaB`                  | `0x3e8b56f1` | Add (New) | Read  |   ✓               |
| `overallCurrentDeltaB`                 | `0xb267ea07` | Add (New) | Read  |   ✓               |
| `scaledDeltaB`                         | `0x24568abf` | Add (New) | Read  |   ✓               |
| `getAmountOut`                         | `0x4aa06652` | Add       | Read  |                   |
| `getMaxAmountIn`                       | `0x24dd285c` | Add       | Read  |                   |

#### L2 ApprovalFacet

Added `ApprovalFacet` address: [`0x0D6dF5E737EF25913F6f2fA1649D0F9530c83D59`](https://arbiscan.io/address/0x0D6dF5E737EF25913F6f2fA1649D0F9530c83D59#code)

| Name                                   | Selector     | Action  | Type  | New Functionality |
|:---------------------------------------|:-------------|:--------|:------|:------------------|
| `approveDeposit`                       | `0x1302afc2` | Add     | Write |                   |
| `decreaseDepositAllowance`             | `0xd9ee1269` | Add     | Write |                   |
| `depositAllowance`                     | `0x2a6a8ef5` | Add     | Read  |                   |
| `depositPermitDomainSeparator`         | `0x8966e0ff` | Add     | Read  |                   |
| `depositPermitNonces`                  | `0x843bc425` | Add     | Read  |                   |
| `increaseDepositAllowance`             | `0x5793e485` | Add     | Write |                   |
| `isApprovedForAll`                     | `0xe985e9c5` | Add     | Read  |                   |
| `permitDeposit`                        | `0x120b5702` | Add     | Write |                   |
| `permitDeposits`                       | `0xd5770dc7` | Add     | Write |                   |
| `setApprovalForAll`                    | `0xa22cb465` | Add     | Write |                   |

#### L2 BDVFacet

Added `BDVFacet` address: [`0xa7D49dC04ab8530509A03f9B8669ac6Bc026711f`](https://arbiscan.io/address/0xa7D49dC04ab8530509A03f9B8669ac6Bc026711f#code)

| Name                                   | Selector     | Action  | Type  | New Functionality |
|:---------------------------------------|:-------------|:--------|:------|:------------------|
| `curveToBDV`                           | `N/A`        | Remove  | Read  |                   |
| `beanToBDV`                            | `0x5a049a47` | Add     | Read  |                   |
| `unripeBeanToBDV`                      | `0xc8cda2a0` | Add     | Read  |                   |
| `unripeLPToBDV`                        | `0xb0c22bb1` | Add     | Read  |                   |
| `wellBdv`                              | `0xc84c7727` | Add     | Read  |                   |

#### L2 GaugePointFacet

Added `GaugePointFacet` address: [`0x043a11704A9e508a2b03c4Dc38Ae60dEE369EAEC`](https://arbiscan.io/address/0x043a11704A9e508a2b03c4Dc38Ae60dEE369EAEC#code)

| Name                                   | Selector     | Action    | Type  | New Functionality |
|:---------------------------------------|:-------------|:----------|:------|:------------------|
| `getExtremelyFarAbove`                 | `0xafa1704e` | Add (New) | Read  |  ✓                |
| `getExtremelyFarBelow`                 | `0xc26072fe` | Add (New) | Read  |  ✓                |
| `getRelativelyFarAbove`                | `0x3ad6d656` | Add (New) | Read  |  ✓                |
| `getRelativelyFarBelow`                | `0x925150c9` | Add (New) | Read  |  ✓                |
| `defaultGaugePointFunction`            | `0xe4b8d822` | Add       | Read  |  ✓                |

#### L2 LiquidityWeightFacet

Added `LiquidityWeightFacet` address: [`0x837B2DB3ea3092E9452fCB118027aeBA1d9FfbD3`](https://arbiscan.io/address/0x837B2DB3ea3092E9452fCB118027aeBA1d9FfbD3#code)

| Name                                   | Selector     | Action    | Type  | New Functionality |
|:---------------------------------------|:-------------|:----------|:------|:------------------|
| `noWeight`                             | `0x225f8659` | Add (New) | Read  |  ✓                |
| `maxWeight`                            | `0x2c5fa218` | Add       | Read  |                   |


#### L2 TractorFacet

Added `TractorFacet` address: [`0xD61E6F775dE1B0C3aC8A4b2516FEb7A935DC85Bb`](https://arbiscan.io/address/0xD61E6F775dE1B0C3aC8A4b2516FEb7A935DC85Bb#code)

| Name                                   | Selector     | Action    | Type  | New Functionality |
|:---------------------------------------|:-------------|:----------|:------|:------------------|
| `cancelBlueprint`                      | `0x563957a8` | Add (New) | Write |  ✓                |
| `getBlueprintHash`                     | `0x5723cc60` | Add (New) | Read  |  ✓                |
| `getBlueprintNonce`                    | `0x5ebc32e6` | Add (New) | Read  |  ✓                |
| `getCounter`                           | `0x5993514b` | Add (New) | Read  |  ✓                |
| `getPublisherCounter`                  | `0x91a45154` | Add (New) | Read  |  ✓                |
| `getTractorVersion`                    | `0x454972dd` | Add (New) | Read  |  ✓                |
| `publishRequisition`                   | `0xcc8a429d` | Add (New) | Write |  ✓                |
| `tractor`                              | `0xfe414fc8` | Add (New) | Write |  ✓                |
| `updatePublisherCounter`               | `0xdf8d26bb` | Add (New) | Write |  ✓                |
| `updateTractorVersion`                 | `0x04cb49dc` | Add (New) | Write |  ✓                |

#### L2 L1ReceiverFacet

Added `L1ReceiverFacet` address: [`0x53106dc7D78dF1EeD36947cf0536d7eCcCa7e0b1`](https://arbiscan.io/address/0x53106dc7D78dF1EeD36947cf0536d7eCcCa7e0b1#code)

| Name                                   | Selector     | Action    | Type   | New Functionality |
|:---------------------------------------|:-------------|:----------|:-------|:------------------|
| `approveReceiver`                      | `0x72138e1a` | Add (New) | Write  |  ✓                |
| `getDepositMerkleRoot`                 | `0x922c0257` | Add (New) | Read   |  ✓                |
| `getFertilizerMerkleRoot`              | `0x2c2eeb72` | Add (New) | Read   |  ✓                |
| `getInternalBalanceMerkleRoot`         | `0x6f5a42e7` | Add (New) | Read   |  ✓                |
| `getPlotMerkleRoot`                    | `0xc4b35a45` | Add (New) | Read   |  ✓                |
| `getReceiver`                          | `0x73139315` | Add (New) | Read   |  ✓                |
| `issueDeposits`                        | `0x6e80f305` | Add (New) | Write  |  ✓                |
| `issueFertilizer`                      | `0xe080f84d` | Add (New) | Write  |  ✓                |
| `issueInternalBalances`                | `0xaf0dc18a` | Add (New) | Write  |  ✓                |
| `issuePlots`                           | `0xba2332df` | Add (New) | Write  |  ✓                |
| `receiveL1Beans`                       | `0x057835e7` | Add (New) | Write  |  ✓                |
| `verifyDepositMerkleProof`             | `0xce2dec6c` | Add (New) | Read   |  ✓                |
| `verifyFertilizerMerkleProof`          | `0xbd1f26a6` | Add (New) | Read   |  ✓                |
| `verifyInternalBalanceMerkleProof`     | `0xe1d8a20f` | Add (New) | Read   |  ✓                |
| `verifyPlotMerkleProof`                | `0x00892531` | Add (New) | Read   |  ✓                |

#### L2 PipelineConvertFacet

Added `PipelineConvertFacet` address: [`0x35f6977D9236C0734520878799598eA0FE692965`](https://arbiscan.io/address/0x35f6977D9236C0734520878799598eA0FE692965#code)

| Name                                   | Selector     | Action    | Type  | New Functionality |
|:---------------------------------------|:-------------|:----------|:------|:------------------|
| `pipelineConvert`                      | `0xbb25c1c2` | Add (New) | Write |   ✓               |

#### L2 ClaimFacet

Added `ClaimFacet` address: [`0xD14b7AB5fd36C770e3339A94F3763cAeC046DDCc`](https://arbiscan.io/address/0xD14b7AB5fd36C770e3339A94F3763cAeC046DDCc#code)

| Name                                   | Selector     | Action    | Type  | New Functionality |
|:---------------------------------------|:-------------|:----------|:------|:------------------|
| `claimAllPlenty`                       | `0xfa2e2617` | Add (New) | Write |  ✓                |
| `claimPlenty`                          | `0x0d509999` | Add       | Write |  ✓                |
| `mow`                                  | `0x150d5173` | Add       | Write |                   |
| `mowMultiple`                          | `0x7d44f5bb` | Add       | Write |                   |
| `plant`                                | `0x779b3c5c` | Add       | Write |                   |

#### L2 OracleFacet

Added `OracleFacet` address: [`0x320AaEBB1a644BEd2B86038eDE49B81072D02be0`](https://arbiscan.io/address/0x320AaEBB1a644BEd2B86038eDE49B81072D02be0#code)

| Name                                   | Selector     | Action    | Type  | New Functionality |
|:---------------------------------------|:-------------|:----------|:------|:------------------|
| `getMillionUsdPrice`                   | `0xd48274a0` | Add (New) | Read  |  ✓                |
| `getRatiosAndBeanIndex`                | `0x052c3990` | Add (New) | Read  |  ✓                |
| `getTokenUsdPrice`                     | `0x00593bcf` | Add (New) | Read  |  ✓                |
| `getTokenUsdPriceFromExternal`         | `0x054ce3bd` | Add (New) | Read  |  ✓                |
| `getTokenUsdTwap`                      | `0xc4c5140f` | Add (New) | Read  |  ✓                |
| `getUsdTokenPrice`                     | `0x8b7750c2` | Add (New) | Read  |  ✓                |
| `getUsdTokenPriceFromExternal`         | `0x399ff0b5` | Add (New) | Read  |  ✓                |
| `getUsdTokenTwap`                      | `0xdd455fbf` | Add (New) | Read  |  ✓                |

### Event Changes from L1 to L2

| Name                                           | Change                                             |
|:-----------------------------------------------|:---------------------------------------------------|
| `UpdatedSeedGaugeSettings`                     | New event                                          |
| `SeedsBalanceChanged`                          | Removed                                            |
| `RemoveWithdrawal`                             | Removed                                            |
| `RemoveWithdrawals`                            | Removed                                            |
| `UpdateGaugeSettings`                          | Removed                                            |
| `FarmerGerminatingStalkBalanceChanged`         | Changed parameter names                            |
| `UpdatedOptimalPercentDepositedBdvForToken`    | New event                                          |
| `UpdatedOracleImplementationForToken`          | New event                                          |
| `UpdatedGaugePointImplementationForToken`      | New event                                          |
| `UpdatedLiquidityWeightImplementationForToken` | New event                                          |
| `WhitelistToken`                               | Liquidity Weight and Gauge Point selectors removed |
| `InternalBalanceChanged`                       | Updated `user` parameter to `account`              |
| `FieldAdded`                                   | New event                                          |
| `ActiveFieldSet`                               | New event                                          |
| `Sow`                                          | Updated with `fieldId` parameter                   |
| `Harvest`                                      | Updated with `fieldId` parameter                   |
| `TemperatureChange`                            | Updated with `fieldId` parameter                   |
| `PodListingCreated`                            | Added `fieldId` and changed other parameter names  |
| `PodListingFilled`                             | Added `fieldId` and changed other parameter names  |
| `PodListingCancelled`                          | Added `fieldId` and changed other parameter names  |
| `PodOrderCreated`                              | Added `fieldId` and changed other parameter names  |
| `PodOrderFilled`                               | Added `fieldId` and changed other parameter names  |
| `PodOrderCancelled`                            | Added `fieldId` and changed other parameter names  |
| `PlotTransfer`                                 | Updated with `fieldId` parameter                   |
| `PodApproval`                                  | Updated with `fieldId` parameter                   |
| `PublishRequisition`                           | New event                                          |
| `CancelBlueprint`                              | New event                                          |
| `Tractor`                                      | New event                                          |
| `ShipmentRoutesSet`                            | New event                                          |
| `UpdatedEvaluationParameters`                  | New event                                          |
| `Receipt`                                      | New event                                          |
| `Shipped`                                      | New event                                          |
| `Receipt`                                      | New event                                          |
| `Reward`                                       | Removed                                            |
| `SeasonOfPlenty`                               | Removed                                            |
| `SeasonOfPlentyWell`                           | New event                                          |
| `SeasonOfPlentyField`                          | New event                                          |
| `MigratedAccountStatus`                        | New event                                          |
| `MigratedPlot`                                 | New event                                          |
| `InternalBalanceMigrated`                      | New event                                          |
| `MigratedPodListing`                           | New event                                          |
| `MigratedPodOrder`                             | New event                                          |
| `AddMigratedDeposit`                           | New event                                          |
| `FertilizerMigrated`                           | New event                                          |
