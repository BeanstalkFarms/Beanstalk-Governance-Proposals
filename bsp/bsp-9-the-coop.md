# BSP-9: Fund the Development and Execution of The Coop

Proposed: December 1, 2022

Status: Proposed

Link: [Snapshot](https://snapshot.org/#/wearebeansprout.eth/proposal/0x44b39b42454e5b50e1da4fb28f2cc2645a4206434debf1109707efc7c741d5ca)

---

- [Proposer](#proposer)
- [Summary](#summary)
- [Purpose](#purpose)
- [Security](#security)
- [Future work](#future-work)
- [Effective](#effective)

## Proposer
Brean and Co.

## Summary
Fund the Coop with 90,000 Beans for development, execution, and seeding of initial capital.

## Purpose
The Coop is an implementation of the Liquity Chicken Bonds on the Beanstalk ecosystem. Chicken Bonds are a novel derivative built on top of liquity that organically builds protocol owned liquidity. Due to the similarities of liquity and beanstalk (namely, protocol native organic yield), we think that beanstalk would be a suitable stablecoin protocol to build chicken bonds on.

Multiple roles will be needed to launch a MVP: 

#### (Essential) Smart Contract Development: 
Currently being developed by Brean and Co.. Coop plans to utilize the ROOT token in order to accelerate development. Current progress is here: https://github.com/Brean0/Beansprout-Coop 

Compensation: 0 Beans will be transferred upon completion.

#### (Essential) Front End Development: 
A web3 Frontend is needed in order to create, view, and interact with the coop bonds. This needs to be composable with both circulating and farmer balances, as well as beanstalk and root. Pipeline can be used to massively improve UX experience (for example, swapping USDC to BEAN, depositing into the silo, minting ROOT, and depositing into a coop bond, all in one transaction).

Compensation: 15,000 Beans will be transferred upon completion.

#### (Non-essential) UX Development:

A good UX design would help facilitate adoption of coop bonds. 

UX Development is labeled “Non-essential” as someone could do both frontend and UX design, as well as utilize available UIs from chicken bonds.

Note: “Nonessential” does not mean “not required”, but “not mandatory” for an MVP. Brean and Co will determine in a project timeline that development has progressed to a point where UX changes would delay the project significantly. 

Compensation: 10,000 Beans will be transferred upon completion.

#### (Non-Essential) NFT Artwork: 

The Coop bonds will implement on-chain dynamic svg NFTs. Current implementation design is here. https://github.com/Brean0/Beansprout-Coop/blob/main/image.png. While this is enough for an MVP, the coop is open for alternative designs. The design must be able to be generated on chain, without the need of a separate contract.

Note: “Non-Essential” does not mean “not required”, but “not mandatory” for an MVP. Brean and Co will determine in a project timeline that development has progressed to a point where UX changes would delay the project significantly. 

Compensation: 5,000 Beans will be transferred upon completion.

Upon deployment of the coop contracts, the remaining unspent beans will be given to the protocol to bootstrap (it will be allocated to the permanent pool, meaning the deposit is forfeited and the yield will permanently be given to bROOT holders). 

## Security
 - Chicken bond audits (Coinspect + Dedaub)
https://github.com/liquity/ChickenBond/tree/main/LUSDChickenBonds/audits
 - Beanstalk Farm plans to allocate an audit stream for the COOP. 

### Risks

- Chicken Bonds are a new and novel implementation, and (at least in beanstalk) has not been tested in the real world. The nature of open-source opens the door for exploits in bugs, flaws, and errors. 
- Coop plans to utilize an upgradeable proxy and a multisig with beanstalk and root community members to enact changes on the will of the token holders, as well as emergency changes. 

## Future work
- Pending chicken bonds could theoretically be used as collateral to borrow BEAN/ROOTs. 
- Analytics can be developed on both the Coop NFT and bROOT token. 
- bROOT/ROOT or bROOT/BEAN could be whitelisted in the silo for stalk rewards, omitting the need for a 3% fee on Chicken In to incentivize liquidity. 
- Discussion on align liquity and the coop. Examples include:
     - Participating in governance to push for a BEAN:LUSD pool. 
     - Allocating a portion of ROOTs in the permanent pool and swap for BEAN:LUSD LP
- Discussion on utilizing ROOTs in permanent pool beyond the native yield
    - Market making on paradox’s parimutuel bets.
    - Purchasing pods on the market.
    - (requires fungible stalk) exchanging stalk for bean, at times of excess demand, or exchanging beans for stalk at times of excess supply. 

## Effective 
Upon passage of this BSP, 90,000 Beans will be sent to the Coop address.

