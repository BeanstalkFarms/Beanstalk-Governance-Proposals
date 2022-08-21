# BPP-0: Next Steps

Proposed: November 23, 2021

Status: Passed for immediate patch

Link: [Snapshot](https://snapshot.org/#/beanstalkfarms.eth/proposal/0xffc6033eb5a4e53f4da5df1c4011bacc12244914885fe11e6a6f2d09d856feed)

---

- [Proposer](#proposer)
- [Proposed Solution](#proposed-solution)
- [Payment of Patch](#payment-of-patch)

## Proposer

Publius

We have identified the issue, and will have a solution ready to go very shortly. An accounting error related to the patch we put up this week caused a misallocation of new Beans minted to the Silo. Every Silo member was affected: every wallet was given either too many or too few Beans. In total, we estimate around 250,000 Beans were misallocated.

## Proposed Solution

- Unpause Beanstalk
- Farmable Seeds will no longer yield Stalk until they are Farmed.
- Over the next few Seasons, the remaining misallocation will be passively corrected. This will consist of all wallets that received too many Beans since the last time they updated their Silo not receiving new Beans for a short period of time, and wallets that received too few Beans since the last time they updated their Silo receiving additional Beans for a short period of time. Based on our rough estimation of the misallocation and the current pace of New Bean mints, we anticipate this will be entirely corrected within < 5 Seasons from Unpause.

This is slightly different than the idea we originally proposed in the Class. Whereas we had originally stated Farmable Seeds will yield grown Stalk, the current proposal is for Farmable Seeds not to yield Stalk until they are Farmed.

This proposal asks whether Stalk holders would prefer Publius to use our ownership privileges to push this fix and immediately resume Beanstalk, or to make the changes and Unpause through a BIP that can be passed in as little as 24 hours from proposal with a supermajority.

To date, we have not pushed any changes to the mechanism using ownership privileges, and are looking to the community for guidance on whether it is preferred to use them to get Beanstalk live ASAP, or to do it through a BIP.

In the coming weeks, as the back-end development capabilities of Beanstalk Farms expand, we can revisit re-improving automatic compounding of Stalk further. We look forward to the community's feedback and appreciate everyone's patience.

## Payment of Patch

In the instance this proposal passes for an immediate patch, we will use funds from the dev budget to fund the patch.
