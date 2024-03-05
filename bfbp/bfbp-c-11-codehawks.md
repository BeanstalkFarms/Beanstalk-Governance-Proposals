# BFBP-C-11: Fund Codehawks Competitive Audit for Seed Gauge System

Proposed: February 20, 2024

Status: Passed

Link: [Snapshot](https://snapshot.org/#/beanstalkfarmsbudget.eth/proposal/0x8c45b4d78404cee03384954f39ac4f9a8ad2f2e8f043c97b4e078c99a4c5bf03)

---

- [Proposer](#proposer)
- [Summary](#summary)
- [Context](#context)
- [Proposal](#proposal)

## Proposer

Guy

## Summary

Fund a [Codehawks](https://www.codehawks.com/) competitive audit of the final version of the Seed Gauge System. Use 94,500 Beans from the Audit Fund to pay for the audit.

## Context

It's no secret that the Seed Gauge System has taken longer to develop and secure than initially expected. For reference, the Cyfrin audit of the Seed Gauge System started in mid-October 2023, which was notably before (1) the 2 critical vulnerabilities reported by whitehats that were fixed in [EBIP-10](https://arweave.net/im3PLE28EkO_eMo4fPmtcTYBJFRErxZ_44I_LWPDIB8) and [EBIP-13](https://arweave.net/zKpHhC4c8NhJecrEGHQ8F_vhrLVVEqdGKrQODSoIKbg) and by definition, before (2) findings from the audit were reported by Cyfrin. Beanstalk Farms paid 60,000 Beans this audit per [BFCP-C-10](https://arweave.net/atWg701gYbp6PoJSK5OlgidojmyPIcPJkXqNwN7rJkg).

As a result of (1), it seems clear that decentralized security programs with aligned incentives for security researchers (like the Immunefi bug bounty program) are essential to securing Beanstalk. However, bug bounty programs typically only secure code after it is deployed on-chain when there are assets at risk. Given the nature of competitive audits, where there are many more auditors and a prize pool that is only rewarded to valid reports, they seem like a preferable pre-deployment alternative to private company audits for securing large, complex codebases like Beanstalk. This was also illustrated by the vulnerabilities found in the [Basin Code4rena audit](https://code4rena.com/reports/2023-07-basin) despite having been audited by 2 private companies beforehand.

As a result of (2), significant design changes to the Seed Gauge System were necessary. In particular, how Beanstalk was measuring Deposited BDV (i.e., what it measures to determine how to allocate Seed rewards to different whitelisted assets) in the initial version of the code was not sufficiently manipulation resistant. Thus, a 2 Season lag in Earned Bean distribution to new Deposits was implemented alongside the removal of the Vesting Period (added in [BIP-36](https://ragdi2zxddzujoy25uj67bzdqglsuvb6y6rxqbvp26rd263oh2sa.arweave.net/iAw0azcY80S7Gu0T74cjgZcqVD7Ho3gGr9eiPXtuPqQ)) altogether. Since then, there have also been additional changes such as infrastructure improvements to the Deposit Whitelist. In total these changes have resulted in >30% of the code in the final version being new or changed since the initial audit. For these reasons, another audit of some form is necessary.

Finally, the Seed Gauge System is complex and is likely to be the most significant change to the Beanstalk codebase in 2024 (amongst [all of the other RFCs](https://github.com/BeanstalkFarms/Beanstalk/issues)). A more proactive approach to security spending (i.e., pre-deployment, rather than post-deployment in the form of bug bounties) is the best path forward.

## Proposal

[Codehawks](https://www.codehawks.com/) is a competitive audit platform facilitated by [Cyfrin](https://cyfrin.io/). 

The audit competition is scheduled to start next week and last 3-4 weeks. The prize pool for the audit will be $90,000. Cyfrin typically charges a 25% platform fee on the prize pool to facilitate Codehawks competitions, but has decreased the fee to 5% for this audit.

After spending the 94,500 Beans proposed in the BFBP, there will be 52,037 Beans remaining in the Audit Fund.
