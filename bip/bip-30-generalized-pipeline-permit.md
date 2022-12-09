# BIP-30: Generalized Pipeline and ERC-20 and ERC-721 Permit Support

Proposed: December 1, 2022

Status: Passed

Link: [Snapshot](https://snapshot.org/#/beanstalkdao.eth/proposal/0x724bbca47b55d42ec25f76c233846bdbbae1dd833618b938c84d58f53ffe7c3d), [Arweave](https://arweave.net/XZuc8TUxvsvl66I1ULw4MBKuku4rJXOwb_Bs7hqMNbQ)

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

Beanstalk Farms

Proposer Wallet: [0xB77c0dBA1186483E9cc284a950564Fe886c12dB8](https://etherscan.io/verifySig/12431)

## Summary

* Implement a version of [Clipboard](https://evmpipeline.org/pipeline.pdf#section.5) in the `farm` function such that Farmers can copy return values from any function call into the function calldata of subsequent functions;
* Wrap [Pipeline](https://evmpipeline.org/pipeline.pdf#section.3) functionality in Depot V2 such that Farmers can access Pipeline through the use of the `farm` function;
* Add [EIP-2612](https://eips.ethereum.org/EIPS/eip-2612) permit support for ERC-20 tokens and [EIP-4494](https://eips.ethereum.org/EIPS/eip-4494) permit support ERC-721 tokens in Circulating balances; and
* Reduce the base `sunrise` incentive reward.

## Links

* [BIP-30 GitHub PR](https://github.com/BeanstalkFarms/Beanstalk/pull/103)
* GitHub Commit Hash: [9029875262f83cf394b0ed048704133e16e969d4](https://github.com/BeanstalkFarms/Beanstalk/pull/103/commits/9029875262f83cf394b0ed048704133e16e969d4)
* [Pipeline Whitepaper](https://evmpipeline.org/pipeline.pdf)
* [Safe Transaction](https://app.safe.global/eth:0xa9bA2C40b263843C04d344727b954A545c81D043/transactions/tx?id=multisig_0xa9bA2C40b263843C04d344727b954A545c81D043_0x94c7c61e9af4591b4a8443371c74c9f6e5b63d2273ff0a70c4ba56b064623722)

## Problem

The `farm` function allows Farmers to perform a series of function calls in a single transaction defined by static calldata. Static calldata is limiting as on-chain outputs can change between transaction creation and settlement.

Currently, adding Pipeline functionality requires new Pipelines to be added to the Depot via Beanstalk governance, which results in very high development overhead for enabling Farmers to use other protocols through Beanstalk.

Farmers have to approve transfers of ERC-20 and ERC-721 tokens in their Circulating balance through a separate transaction, which creates a suboptimal user experience.

Beanstalk is currently overpaying for the `sunrise` incentive reward due to the high base incentive.

## Proposed Solution

### Clipboard

We propose upgrading the Farm Facet to support [Clipboard](https://evmpipeline.org/pipeline.pdf#section.5) functionality from Pipeline, allowing Farmers to copy return values from any function call into the function calldata of subsequent functions.

#### Specification

`AdvancedFarmCall` is a `farm` call that can use a version of Clipboard. With Clipboard, Farmers can copy return values stored as `returnData` from any `AdvancedFarmCalls` already executed and paste them into the `callData` of the next `AdvancedFarmCall`, in a customizable manner.

Each `AdvancedPipeCall` includes a version of Clipboard to encode:
1. How many paste operations to perform;
2. Which `returnData` from previous `AdvancedFarmCall`s to copy (i.e., `returnDataIndex`);
3. Where to copy from within (2) (i.e., `copyIndex`); and 
4. Where to paste it in the `callData` of the next
`AdvancedFarmCall` (i.e., `pasteIndex`). 

Bytes are pasted 32 bytes at a time. 

The only difference between the Clipboard implemented on the Farm and the one implemented in Pipeline is that the Farm version always uses padding in the second byte, whereas the Pipeline version includes a `Use Ether Flag` in the second byte, which specifies whether to include Ether in the Clipboard. Because the `farm` call already is able to include Ether, the second byte does not need to be used in the Farm version of Clipboard. 

### Depot V2

We propose adding new Depot Facet that wraps the standalone [Pipeline](https://evmpipeline.org) contract, providing access to Pipeline from Beanstalk through the use of the `farm` function.

### ERC-20 and ERC-721 Token Permits

We propose adding [EIP-2612](https://eips.ethereum.org/EIPS/eip-2612) support for ERC-20 tokens and [EIP-4494](https://eips.ethereum.org/EIPS/eip-4494) support for ERC-721 tokens from Circulating balances, which allows Farmers to perform approvals through permits without the need for a separate transaction.

### Sunrise Incentive Adjustment

We propose reducing the base `sunrise` incentive from 100 Beans to 25 Beans such that the new formula for $a_t$ is:

$$a_t = 25 \times 1.01^{\text{min}\{{E_t - E_{t}^{\text{min}}},\ 300\}}$$

## Technical Rationale

Allowing Farmers to copy return values from any function call into the function calldata of subsequent functions significantly improves the composability supported by the `farm` function.

A new Depot that allows Farmers to access the standalone Pipeline contract from Beanstalk significantly reduces the development time necessary to enable Farmers to interact with new protocols via Beanstalk.

The ability for Farmers to approve transfers of ERC-20 and ERC-721 tokens in their Circulating balance without the need for a separate transaction reduces the friction of interacting with Beanstalk.

The majority of the transaction fee from `sunrise` calls is the priority fee. Until a more sophisticated update to the `sunrise` function has been audited, reducing the base reward is likely to reduce the Beans Beanstalk issues each month to pay for the `sunrise` call by up to 75%.

## Economic Rationale

Improving the composability of, and reducing the number of separate transactions required to interact with, Beanstalk should improve the user experience and utility of Beanstalk and Beans.

Reducing unnecessary Bean issuance in the `sunrise` incentive reward can reduce sell pressure on Beans by 60,000 per month. 

## Contract Changes

### Farm Facet

The following `FarmFacet` is being removed from Beanstalk:
* [`0x6039c602b730f44f418145454a2d954133cbd394`](https://etherscan.io/address/0x6039c602b730f44f418145454a2d954133cbd394#code)

The following `FarmFacet` is being added to Beanstalk:
* [`0x855D37a6C3868Aa4e8F2e1a80965D08B3f10d292`](https://etherscan.io/address/0x855D37a6C3868Aa4e8F2e1a80965D08B3f10d292#code)

#### `FarmFacet` Function Changes

| Function Name  | Selector      |  Action   | Type | New Functionality |
|:---------------|:--------------|:----------|:-----|:------------------|
| `advancedFarm` | `0x36bfafbd`  |  Add      | Call |  &check;          |
| `farm`         | `0x300dd6cf`  |  Replace  | Call |  &check;          |

#### `FarmFacet` Event Changes

None.

### Depot Facet

The following `DepotFacet` is being added to Beanstalk:
* [`0xD812fDfB45BC4D05884Eb270f7DdFaac71D60F78`](https://etherscan.io/address/0xD812fDfB45BC4D05884Eb270f7DdFaac71D60F78#code)

#### `DepotFacet` Function Changes

|      Name       |   Selector     | Action |  Type  | New Functionality |
|:----------------|:---------------|:-------|:-------|:------------------|
| `pipe`          | `0x08e1a0ab`   |  Add   |  Call  |      &check;      |
| `multiPipe`     | `0xcabec62b`   |  Add   |  Call  |      &check;      |
| `advancedPipe`  | `0xb452c7ae`   |  Add   |  Call  |      &check;      |
| `etherPipe`     | `0x6e47d07b`   |  Add   |  Call  |      &check;      |
| `readPipe`      | `0xdd756c4f`   |  Add   |  View  |      &check;      |

#### `DepotFacet` Event Changes

None.

### Token Support Facet

The following `TokenSupportFacet` is being added to Beanstalk:
* [`0x5e15667Bf3EEeE15889F7A2D1BB423490afCb527`](https://etherscan.io/address/0x5e15667Bf3EEeE15889F7A2D1BB423490afCb527#code)

#### `TokenSupportFacet` Function Changes

|          Name           |    Selector    | Action | Type | New Functionality |
|:------------------------|:---------------|:-------|:-----|:------------------|
| `permitERC20`           | `0xb442b398`   |  Add   | Call |     &check;       |
| `transferERC721`        | `0x1aca6376`   |  Add   | Call |     &check;       |
| `permitERC721`          | `0x4935ed43`   |  Add   | Call |     &check;       |
| `transferERC1155`       | `0x0a7e880c`   |  Add   | Call |     &check;       |
| `batchTransferERC1155`  | `0xa9412a59`   |  Add   | Call |     &check;       |

#### `TokenSupportFacet` Event Changes

None.

### Season Facet

The following `SeasonFacet` is being removed from Beanstalk:
* [`0x83d6e6b446613c9bfaebc64260962bc4f828a3ac`](https://etherscan.io/address/0x83d6e6b446613c9bfaebc64260962bc4f828a3ac#code)

The following `SeasonFacet` is being added to Beanstalk:
* [`0x0cEFF1129091A0ffa97cC58d4D160F9676866a24`](https://etherscan.io/address/0x0cEFF1129091A0ffa97cC58d4D160F9676866a24#code)

#### `SeasonFacet` Function Changes

| Function Name   | Selector      |  Action   | Type | New Functionality |
|:----------------|:--------------|:----------|:-----|:------------------|
| `sunrise`       | `0xfc06d2a6`  |  Replace  | Call |                   |
| `paused`        | `0x5c975abb`  |  Replace  | View |                   |
| `plentyPerRoot` | `0xe60d7a83`  |  Replace  | View |                   |
| `poolDeltaB`    | `0x471bcdbe`  |  Replace  | View |                   |
| `rain`          | `0x43def26e`  |  Replace  | View |                   |
| `season`        | `0xc50b0fb0`  |  Replace  | View |                   |
| `seasonTime`    | `0xca7b7d7b`  |  Replace  | View |                   |
| `time`          | `0x16ada547`  |  Replace  | View |                   |
| `totalDeltaB`   | `0x06c499d8`  |  Replace  | View |                   |
| `weather`       | `0x686b6159`  |  Replace  | View |                   |
| `yield`         | `0x28593984`  |  Replace  | View |                   |

#### `SeasonFacet` Event Changes

None.

## Beans Minted

None.

## Audit

The commit hash of this BIP is [**9029875262f83cf394b0ed048704133e16e969d4**](https://github.com/BeanstalkFarms/Beanstalk/pull/103/commits/9029875262f83cf394b0ed048704133e16e969d4).

Halborn has performed an audit of this BIP up until the commit hash that reduces the base `sunrise` incentive reward. You can view the Halborn audit report of this commit hash on Arweave here: 
* [**12/01/22 BIP-30 Halborn Report**](https://arweave.net/qAHdIuhoRN5fffERUyptDNf9XQXJqApgyzvgTZRiH24)

## Effective

Effective immediately upon commit.
