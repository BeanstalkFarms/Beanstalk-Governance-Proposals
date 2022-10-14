# BFBP-C-1: Bug Bounty Award to sync

Proposed: September 12, 2022

Status: Passed

Link: [Snapshot](https://snapshot.org/#/beanstalkfarmsbudget.eth/proposal/0xc3c20ab43555355e76badb699086c1beff0ee0c38b526a680d13408983b03933), [Arweave](https://arweave.net/GmlK5RaHU3cD4914pAhgVmJej5S1evHEISh-tOYDsRs)

---

- [Proposer](#proposer)
- [Proposal](#proposal)
- [Rationale](#rationale)
- [Vulnerability](#vulnerability)
- [Payment](#payment)

## Proposer

mod323

## Proposal

Pay a bug bounty to sync for his discovery of an on-chain TWAP oracle issue that Beanstalk may become vulnerable to after the Ethereum Merge.

## Rationale

sync reached out to Publius with a potential vulnerability. As a formal bug bounty program is not yet live, we have offered sync an unofficial bug bounty of 15,000 Beans for their efforts.

## Vulnerability

After the Ethereum Merge occurs, multi-block MEV will be possible, allowing validators to manipulate TWAP oracles by moving the price orders of magnitude higher for at least 1 block in a risk free fashion by either adding 1-sided liquidity and/or buying all the Beans in the pool. For more information see here: https://chainsecurity.com/oracle-manipulation-after-merge/.

Beanstalk currently uses a time weighted average oracle over the course of an hour to calculate deltaB, which determines the amount of Beans or Soil to mint each Season. Thus, node operators will have the potential to manipulate the number of Beans/Soil minted during a Season as soon as the merge happens.

For more information on the problem and proposed solution, see the following links on GitHub:

- GitHub issue: https://github.com/BeanstalkFarms/Beanstalk/issues/91
- GitHub Pull Request: https://github.com/BeanstalkFarms/Beanstalk/pull/92

## Payment

15,000 Beans
