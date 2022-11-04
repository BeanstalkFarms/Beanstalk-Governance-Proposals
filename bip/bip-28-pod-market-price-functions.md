# BIP-28: Pod Market Price Functions

Proposed: November 1, 2022

Status: Cancelled (A majority of the BCM Signers deemed a bug report valid by submitting and signing an on-chain rejection, see [this verified signature](https://etherscan.io/verifySig/11749) by one of the Signers).

Link: [Arweave](https://arweave.net/nAS1_yr86Lrp_GphZLfIU-AU1un-fw-gF5ATUhNpgjk)

---

- [Proposer](#proposer)
- [Summary](#summary)
- [Links](#links)
- [Quorum](#quorum)
- [Problem](#problem)
- [Proposed Solution](#proposed-solution)
- [Technical Rationale](#technical-rationale)
- [Economic Rationale](#economic-rationale)
- [Contract Changes](#contract-changes)
- [Audit](#audit)
- [Effective](#effective)

## Proposer

Beanstalk Farms

Proposer Wallet: [0x36998db3f9d958f0ebaef7b0b6bf11f3441b216f](https://etherscan.io/verifySig/11743)

## Summary

* Implement V2 Pod Orders and Listings such that the Price per Pod is priced as a function of place in the Pod Line (via piecewise cubic polynomials);
* Allow Farmers to delegate use of their Farm balances to other contracts and add [EIP-2612](https://eips.ethereum.org/EIPS/eip-2612) permit support to Farm balances; and
* Add [EIP-2612](https://eips.ethereum.org/EIPS/eip-2612) permit support for Silo Deposits.

## Links

* [BIP-28 GitHub PR](https://github.com/BeanstalkFarms/Beanstalk/pull/87)
    * [RFC: Pod Market V2 (Price Functions)](https://github.com/BeanstalkFarms/Beanstalk/issues/88)
    * [RFC: ERC-20 Farm Balance Approval System and Permit Implementation](https://github.com/BeanstalkFarms/Beanstalk/issues/129)
    * [RFC: Silo Deposit Permit Implementation](https://github.com/BeanstalkFarms/Beanstalk/issues/128)
* [GitHub Commit Hash](https://github.com/BeanstalkFarms/Beanstalk/commit/b6a567d842e72c73176099ffd8ddb04cae2232e6)
* [Gnosis Transaction](https://gnosis-safe.io/app/eth:0xa9bA2C40b263843C04d344727b954A545c81D043/transactions/multisig_0xa9bA2C40b263843C04d344727b954A545c81D043_0xc9f799139e496e05b8a1d222d973c26ed428aef7f13f5a7de7e12db75c4f97f8)

## Quorum

Quorum is a majority of the Stalk supply voting For, or about **33,624,397** Stalk voting For based on the time of proposal.

## Problem

The Pod Market currently limits Farmers to creating Pod Orders and Listings with a single Fill price per Pod independent of place in Line. 

Because of the ordinal nature of Pods, the price per Pod is partially a function of place in Line. Pod Orders and Listings with a single Fill price fail to maximize overall marketplace liquidity by requiring the placing or updating of multiple orders in order to create a non-flat pricing curve, which is highly expensive for users. 

Currently, there is no way for Farmers to delegate use of their Farm balances to other addresses; the only way to transfer Farm balances is by calling `transferToken(s)()` through the Farmer's account.

Currently, a separate transaction is required in order for a Farmer to delegate another address to use their Silo Deposits.

## Proposed Solution

### Pod Market Price Functions

We propose the implementation of new V2 Pod Orders and Listings where the Price Per Pod is priced as a piecewise function of Place in Line of up to `n` cubic polynomial pieces. A new library, `LibPolynomial.sol`, is introduced to evaluate and integrate over piecewise cubic polynomials on-chain in a gas efficient fashion to facilitate dynamic pricing of V2 Pod Orders and Listings. 

Implementing dynamically priced Pod Orders and Listings should significantly improve market efficiency by allowing Farmers to place and update prices across the entire Pod Line is a single Order or Listing. 

#### Specification

The `LibPolynomial.sol` library is introduced, which contains the logical functionality required to store, integrate and evaluate price functions for use within the `MarketplaceFacet`. 

Price functions as implemented in `LibPolynomial.sol` are represented as **piecewise cubic polynomials**. Each piece can represent any polynomial function of up to degree 3. There is no maximum number of pieces that can be contained within a single piecewise function. 

##### Representation of Piecewise Cubic Polynomials

The data required to represent a piecewise cubic polynomial consists of the domain of each piece as well as the coefficient for each polynomial term (up to 4 terms in a cubic). The function domains are represented as an array of `uint256` breakpoints in ascending order of domain. Each polynomial's domain is inclusive at the start and exclusive at the end. The final polynomial's domain starts at the final breakpoint and extends until infinity. 

##### Floating Point Representation of Polynomial Coefficients

The polynomial coefficients are stored in floating point representation and separated into three parts: the significand (numerator), the exponent of base 10 that the significand is shifted by, and the sign of the coefficient, where true is positive. For example, the decimal number 0.1234 could be represented as:
- **significand**: 1234
- **exponent**: 4
- **sign**: true

##### Byte Encoding of Function Data

V2 Orders and Listings use a dynamically sized `bytes` array to represent pricing functions in the transaction data allowing its data to be packed tightly without posing any limitation on the number of pieces allowed. All function data is concatenated into a bytes array that is divided up into sections. Data is ordered first by piecewise domain, and second by degree. 

The first 32 bytes represent the number of polynomial pieces in the function array, which is then used to determine the length of the following sections of data. If there are `n` number of pieces in the function, the next 32n bytes following the length are occupied by the breakpoints. The following 128n bytes are occupied by the function coefficient significands. The following 4n bytes contain the coefficient exponents. And the final 4n bytes contain the coefficient signs. 

<img width="948" alt="image" src="https://user-images.githubusercontent.com/19599875/190914665-3e6a472e-9fee-4164-aaf6-8f8ea7def11e.png">

##### Storage

`mapping(bytes32 => uint256) podOrders;` Has been changed to store the amount of Beans to be used by a Pod Order rather than the amount of Pods requested by it. This is because the amount of Pods an Order will Fill is now variable.

### Farm Balance Approval System and Permits

We propose adding an approval system to Farm balances, which allows Farmers to delegate their Farm balances to be used by other addresses.

We also propose adding [EIP-2612](https://eips.ethereum.org/EIPS/eip-2612) support for Farm balances, which allows Farmers to delegate use of their Farm balances through permits without the need for a separate transaction.

### Silo Deposit Permits

We propose adding [EIP-2612](https://eips.ethereum.org/EIPS/eip-2612) support for Silo Deposits, which allows Farmers to delegate use of their Silo Deposits through permits without the need for a separate transaction.

## Technical Rationale

Dynamically priced Pod Orders and Pod Listings reduce the user action (and associated gas costs) required to make or update sophisticated pricing curves on the Pod Market to a single transaction.

The ability for Farmers to delegate their Farm balances to another contract allows other protocols to build on top of Beanstalk's Farm balance system. The ability to perform this delegation without a separate transaction reduces the friction of interacting with Beanstalk.

Similarly, the ability for Farmers to delegate their Silo Deposits to another contract without the need for a separate transaction reduces the friction of interacting with Beanstalk. 

## Economic Rationale

Implementing dynamically priced Pod Orders and Listings should improve market efficiency and depth.

Improving the composability of and reducing the number of separate transactions required to interact with Beanstalk should improve the utility of Beanstalk and Beans.

## Contract Changes

### MarketplaceFacet

The following `MarketplaceFacet`(s) are being deprecated:
* [`0x3600D953Cb26C75E8F0c76FcB20e3A8F8a3245F1`](https://etherscan.io/address/0x3600D953Cb26C75E8F0c76FcB20e3A8F8a3245F1#code)
* [`0xD870aAB97c2739b320a3eFAd370511452894F1b2`](https://etherscan.io/address/0xD870aAB97c2739b320a3eFAd370511452894F1b2#code)

The following `MarketplaceFacet` is being added to Beanstalk:
* [`0xF5f0c743573fBAe3A76892B03f70596526F4E1D5`](https://etherscan.io/address/0xF5f0c743573fBAe3A76892B03f70596526F4E1D5#code)

#### `MarketplaceFacet` Function Changes

| Function Name                         | Selector       | Action    | New Functionality |
| :------------------------------------ | :------------- | :-------- | :---------------- |
| `cancelPodOrder(...)`                 | `0xdf18a3ee`   |  Add      |  &check;          |
| `cancelPodOrderV2(...)`               | `0xf22b49ec`   |  Add      |  &check;          |
| `createPodListing(...)`               | `0x80bd7d33`   |  Add      |  &check;          |
| `createPodListingV2(...)`             | `0xa8f135a2`   |  Add      |  &check;          |
| `createPodOrder(...)`                 | `0x82c65124`   |  Add      |  &check;          |
| `createPodOrderV2(...)`               | `0x83601992`   |  Add      |  &check;          |
| `fillPodListing(...)`                 | `0xeda8156e`   |  Add      |  &check;          |
| `fillPodListingV2(...)`               | `0xa99d840c`   |  Add      |  &check;          |
| `fillPodOrder(...)`                   | `0x845a022b`   |  Add      |  &check;          |
| `fillPodOrderV2(...)`                 | `0x4214861e`   |  Add      |  &check;          |
| `getAmountBeansToFillOrderV2(...)`    | `0x7e2a1fd1`   |  Add      |  &check;          |
| `getAmountPodsFromFillListingV2(...)` | `0xc3e14715`   |  Add      |  &check;          |
| `podOrder(...)`                       | `0x042ff31d`   |  Add      |  &check;          |
| `podOrderV2(...)`                     | `0x045d5763`   |  Add      |  &check;          |
| `allowancePods(...)`                  | `0x0b385a85`   |  Replace  |                   |
| `approvePods(...)`                    | `0xc5644a60`   |  Replace  |                   |
| `cancelPodListing(...)`               | `0x3260c49e`   |  Replace  |                   |
| `podListing(...)`                     | `0xd6af17ab`   |  Replace  |                   |
| `getPodOrderById(...)`                | `0xb1719e59`   |  Replace  |                   |
| `transferPlot(...)`                   | `0x69d9120d`   |  Replace  |                   |
| `cancelPodOrder(...)`                 | `0xeb6fa84f`   |  Remove   |                   |
| `createPodListing(...)`               | `0xed778f8e`   |  Remove   |                   |
| `createPodOrder(...)`                 | `0x72db799f`   |  Remove   |                   |
| `podOrder(...)`                       | `0x56e70811`   |  Remove   |                   |
| `fillPodOrder(...)`                   | `0x6d679775`   |  Remove   |                   |
| `fillPodListing(...)`                 | `0x1aac9789`   |  Remove   |                   |

#### `MarketplaceFacet` Event Changes

| Event Name                         | Change                                                             |
| :--------------------------------- | :----------------------------------------------------------------- |
| `PodOrderCreated(...)`             | New parameter(s): `minFillAmount`, `pricingFunction`, `priceType`  |
| `PodListingCreated(...)`           | New parameter(s): `minFillAmount`,`pricingFunction`, `pricingType` |
| `PodListingFilled(...)`            | New parameter(s): `costInBeans`                                    |
| `PodOrderFilled(...)`              | New parameter(s): `costInBeans`                                    |

### TokenFacet

The following `TokenFacet` is being deprecated:
* [`0x146f86c2EF039f9176bc2434D3DA5919C19B87fC`](https://etherscan.io/address/0x146f86c2EF039f9176bc2434D3DA5919C19B87fC#code)

The following `TokenFacet` is being added to Beanstalk:
* [`0x8D00eF08775872374a327355FE0FdbDece1106cF`](https://etherscan.io/address/0x8D00eF08775872374a327355FE0FdbDece1106cF#code)

#### `TokenFacet` Function Changes

| Function Name                         | Selector       | Action    | New Functionality |
| :------------------------------------ | :------------- | :-------- | :---------------- |
| `approveToken(...)`                   | `0xda3e3397`   |  Add      |  &check;          |
| `decreaseTokenAllowance(...)`         | `0x0bc33ce4`   |  Add      |  &check;          |
| `increaseTokenAllowance(...)`         | `0xb39062e6`   |  Add      |  &check;          |
| `permitToken(...)`                    | `0x7c516e94`   |  Add      |  &check;          |
| `tokenAllowance(...)`                 | `0x8e8758d8`   |  Add      |  &check;          |
| `tokenPermitDomainSeparator(...)`     | `0x1f351f6a`   |  Add      |  &check;          |
| `tokenPermitNonces(...)`              | `0x4edcab2d`   |  Add      |  &check;          |
| `transferTokenFrom(...)`              | `0x7006e387`   |  Add      |  &check;          |
| `getAllBalance(...)`                  | `0x0b385a85`   |  Replace  |                   |
| `getAllBalances(...)`                 | `0xb6fc38f9`   |  Replace  |                   |
| `getBalance(...)`                     | `0xd4fac45d`   |  Replace  |                   |
| `getBalances(...)`                    | `0x6a385ae9`   |  Replace  |                   |
| `getExternalBalance(...)`             | `0x4667fa3d`   |  Replace  |                   |
| `getExternalBalances(...)`            | `0xc3714723`   |  Replace  |                   |
| `getInternalBalance(...)`             | `0x8a65d2e0`   |  Replace  |                   |
| `getInternalBalances(...)`            | `0xa98edb17`   |  Replace  |                   |
| `transferToken(...)`                  | `0x6204aa43`   |  Replace  |  &check;          |
| `unwrapEth(...)`                      | `0xbd32fac3`   |  Replace  |                   |
| `wrapEth(...)`                        | `0x1c059365`   |  Replace  |                   |

#### `TokenFacet` Event Changes

| Event Name                         | Change       |
| :--------------------------------- | :----------- |
| `TokenApproval(...)`               | New event    |

### SiloFacet

The following `SiloFacet` is being deprecated:
* [`0x6530A76c77F11731Bf7F1C799AA97E0C15d3FB26`](https://etherscan.io/address/0x6530A76c77F11731Bf7F1C799AA97E0C15d3FB26#code)

The following `SiloFacet` is being added to Beanstalk:
* [`0xf73db3fb33c7070db0f0ae4a76872251dca15e97`](https://etherscan.io/address/0xf73db3fb33c7070db0f0ae4a76872251dca15e97#code)

#### `SiloFacet` Function Changes

| Function Name                         | Selector       | Action    | New Functionality |
| :------------------------------------ | :------------- | :-------- | :---------------- |
| `depositPermitDomainSeparator()`      | `0x8966e0ff`   |  Add      |  &check;          |
| `depositPermitNonces(...)`            | `0x843bc425`   |  Add      |  &check;          |
| `permitDeposit(...)`                  | `0x120b5702`   |  Add      |  &check;          |
| `permitDeposits(...)`                 | `0xd5770dc7`   |  Add      |  &check;          |
| `approveDeposit(...)`                 | `0x1302afc2`   |  Replace  |                   |
| `balanceOfEarnedBeans(...)`           | `0x3e465a2e`   |  Replace  |                   |
| `balanceOfEarnedSeeds(...)`           | `0x602aa123`   |  Replace  |                   |
| `balanceOfEarnedStalk(...)`           | `0x341b94d5`   |  Replace  |                   |
| `balanceOfGrownStalk(...)`            | `0x249564aa`   |  Replace  |                   |
| `balanceOfPlenty(...)`                | `0x896651e8`   |  Replace  |                   |
| `balanceOfRainRoots(...)`             | `0x69fbad94`   |  Replace  |                   |
| `balanceOfRoots(...)`                 | `0xba39dc02`   |  Replace  |                   |
| `balanceOfSeeds(...)`                 | `0x4916bc72`   |  Replace  |                   |
| `balanceOfSop(...)`                   | `0xa7bf680f`   |  Replace  |                   |
| `balanceOfStalk(...)`                 | `0x8eeae310`   |  Replace  |                   |
| `claimPlenty()`                       | `0x45947ba9`   |  Replace  |                   |
| `claimWithdrawal(...)`                | `0x488e94dc`   |  Replace  |                   |
| `claimWithdrawals(...)`               | `0x764a9874`   |  Replace  |                   |
| `decreaseDepositAllowance(...)`       | `0x82c65124`   |  Replace  |                   |
| `deposit(...)`                        | `0xf19ed6be`   |  Replace  |                   |
| `depositAllowance(...)`               | `0x2a6a8ef5`   |  Replace  |                   |
| `enrootDeposit(...)`                  | `0xd5d2ea8c`   |  Replace  |                   |
| `enrootDeposits(...)`                 | `0x83b9e85d`   |  Replace  |                   |
| `getDeposit(...)`                     | `0x8a6a7eb4`   |  Replace  |                   |
| `getTotalDeposited(...)`              | `0x0c9c31bd`   |  Replace  |                   |
| `getTotalWithdrawn(...)`              | `0xb1c7a20f`   |  Replace  |                   |
| `getWithdrawal(...)`                  | `0xe23c96a4`   |  Replace  |                   |
| `increaseDepositAllowance(...)`       | `0x5793e485`   |  Replace  |                   |
| `lastSeasonOfPlenty()`                | `0xbe6547d2`   |  Replace  |                   |
| `lastUpdate(...)`                     | `0xcb03fb1e`   |  Replace  |                   |
| `plant()`                             | `0x779b3c5c`   |  Replace  |                   |
| `tokenSettings(...)`                  | `0xe923e8d4`   |  Replace  |                   |
| `totalEarnedBeans(...)`               | `0xfd9de166`   |  Replace  |                   |
| `totalRoots()`                        | `0x46544166`   |  Replace  |                   |
| `totalSeeds()`                        | `0xd8bd0d9d`   |  Replace  |                   |
| `totalStalk()`                        | `0x7b52fadf`   |  Replace  |                   |
| `transferDeposit(...)`                | `0x9e32d261`   |  Replace  |                   |
| `transferDeposits(...)`               | `0x0d2615b1`   |  Replace  |                   |
| `update(...)`                         | `0x1c1b8772`   |  Replace  |                   |
| `withdrawDeposit(...)`                | `0x7af9a0ce`   |  Replace  |                   |
| `withdrawDeposits(...)`               | `0xb189d9c8`   |  Replace  |                   |
| `withdrawFreeze(...)`                 | `0x55926690`   |  Replace  |                   |

## Audit

The commit hash of this BIP is [b6a567d842e72c73176099ffd8ddb04cae2232e6](https://github.com/BeanstalkFarms/Beanstalk/commit/b6a567d842e72c73176099ffd8ddb04cae2232e6).

Halborn has performed an audit of this commit hash. You can view the Halborn audit report of this commit hash on Arweave here: https://arweave.net/eLFWy47yKtbwnhMgmtdbdcRbxwaXPJ4BB0LiEzx8hZg

## Effective

Effective immediately upon commit.
