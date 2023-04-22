# BSP-10: Award Pharos Protocol a Grant to Develop a Pod Lending Market

Proposed: April 17, 2023

Status: Passed

Link: [Snapshot](https://snapshot.org/#/wearebeansprout.eth/proposal/0x27de7989b73cf3a759b8bd9b321a07239cf3289cb03d88846851535e4cf0444e), [Arweave](https://arweave.net/sTthYixn4VOwaJB2wy6YxwWF4H2tnGl_gyLpm9r3sIA)

---

- [Proposer](#proposer)
- [Summary](#summary)
- [Purpose](#purpose)
- [Effective](#effective)

## Proposer

Mod323 and Funderberker (co-founders of Pharos)

## Summary

[Pharos](https://www.pharosprotocol.com/) is an on-chain prime brokerage protocol that enables users to access, trade, and deploy leveraged positions with any on-chain asset or protocol.

This proposal proposes to reallocate 25,000 Beans from [BSP-9](https://app.bean.money/#/governance/0x44b39b42454e5b50e1da4fb28f2cc2645a4206434debf1109707efc7c741d5ca) to Pharos for the development and deployment of a lending market for Pods.

## Purpose

The [Market](https://app.bean.money/#/market/buy) facilitates the exchange of Pods in the secondary market. A Pod lending market deployed by Pharos would enable using Pods as collateral against loans.

Multiple roles will be needed to launch a MVP:

#### Smart Contract Development:

The Pharos core smart contracts are in development. To facilitate Pod lending, a Pod oracle and liquidation mechanism needs to be developed.


#### Front End Development:

A frontend is needed in order to create, view, and interact with Pharos.

### Example

Borrower-A, a Beanstalk Farmer, takes out two loans: Position-1 from Lender-A and Position-2 from Lender-B. 

Position-1 is an under-collateralized loan where the borrower took out a USDC loan and swapped it for ETH on Uniswap, effectively longing ETH with leverage. 

Position-2 is an over-collateralized loan where the borrower borrowed ETH.

![image](https://arweave.net/dA8AAzMgM0PqoGAY1VZwtDzMKFtwkELnHLov5B3v8AQ)

### Risks

Pharos is a new protocol that is yet to launch. The goal would be to have the code audited before deployment (no guarantee), but this grant will not cover an audit. 

## Effective

Upon passage of this BSP, 25,000 Beans will be sent from the Coop to Pharos.
