# BIR-1: Pod Listing Cancellation

Proposed: October 25, 2022

Status: Proposed

Link: [Snapshot](https://snapshot.org/#/beanstalkbugbounty.eth/proposal/0x1da231494fe8cf85edc50bf148b8557b3de8b0354018602b92075634d0e1f409)

---

- [Proposer](#proposer)
- [BIR Process Note](#bir-process-note)
- [Bug](#bug)
- [Fix](#fix)
- [Determination](#determination)
- [Bounty Amount](#bounty-amount)


## Proposer

Beanstalk Immunefi Committee

## BIR Process Note

Once a BIR passes, the Beanstalk Community Multisig (BCM) executes it by:
- Minting the corresponding number of Beans to cover the bug bounty and the 10% fee from Immunefi;
- Transferring the Beans corresponding to the bounty to the submitting party’s address; and
- Transferring the Beans corresponding to the 10% fee to Immunefi’s address.

## Bug

Farmers could cancel Pod Listings on behalf of Farmers by calling the `fillPodListing(...)` function with an input `beanAmount = 0`.

## Fix

Add the following check: `require(amount > 0, "Marketplace: Must fill > 0 Pods.");`.

The fix has been reviewed by Halborn.

See [EBIP-3: Pod Listing Cancellation](https://arweave.net/OftwDeHeyC61Xe7nVjBpQITh7T3m08-hXF9sQ9TjMfs).

## Determination

* Potential practicable economic damage: This bug would not have resulted in any loss of funds.
* Impact: Medium
* Entitled to reward: Yes

## Bounty Amount

7,500 Beans. A total of 8,250 Beans will be minted by the BCM in order to cover both the bug bounty and the 10% Immunefi fee.
