# BFBP-A-6: Hire TBIQ

Proposed: August 24, 2022

Status: Passed

Link: [Snapshot](https://snapshot.org/#/beanstalkfarmsbudget.eth/proposal/0x155e6e34b1213a0f0fcfe6548d4f4128ce610f32c5cf651920af3d0bd9d24363)

---

- [Proposer](#proposer)
- [Summary](#summary)
- [Background](#background)
- [Past Contributions to Beanstalk](#past-contributions-to-beanstalk)
- [Role](#role)
- [Payment](#payment)
- [Commitment](#commitment)

## Proposer

Silo Chad

## Summary

Hire TBIQ part-time as Data Science Lead.

## Background

TBIQ is a machine learning engineer specializing in data science, prior to which they were in academia researching the intersection of software engineering and data visualization. 

## Past Contributions to Beanstalk

TBIQ was originally hired as Data Science Lead for Beanstalk Farms in late Q1 2022. Contributions since then include:
* [Pre-exploit Dune dashboard](https://dune.com/tbiq/Beanstalk) 
    * Created all queries and visualizations available on the official Beanstalk Dune dashboard, which provides a comprehensive data-driven view of the pre-exploit Beanstalk ecosystem. 
    * Adjusted the dashboard to only show pre-exploit data in order to serve as a historical reference.
* [Pod Market yield curve modeling](https://github.com/TBIQ/pod-market-yc)
    * Modeled historical Pod Market data, exploring the evolution of the Pod Market yield curve (Place in Line vs. price per Pod) over time. This work served as the basis for an early stage proposal to [FIAT DAO](https://fiatdao.com/) to enable Pods as a collateral type for lending and borrowing within their protocol. This work was halted due to the exploit, but may make sense to pick back up once there is a more efficient Pod Market post-Replant. 
* [Silo APY calculator ](https://dune.com/tbiq/Beanstalk-Silo-APY-Calculator)
    * Designed and built a Silo APY calculator that provides Farmers with historical context and allows them to make their own assumptions about future variable trends. 
* Ad-hoc data requests
    * Served as the point of contact for surfacing on-chain metrics for one-off analyses, for both community members and Beanstalk Farms contributors (eligible addresses for Barn Raise BeaNFTs, internal analysis of variables driving demand for Soil, etc.).
* [Barn Raise Dune dashboard](https://dune.com/tbiq/beanstalk-barn-raise)
    * Created a dashboard for the Barn Raise that was widely used among the community to monitor and understand progress. 
* State computation and cross validation for the Replant 
    * Leveraged Token Flow, a database product which can be used to query the storage level data of smart contracts over time, to write SQL queries to compute the state of Beanstalk at the pre-exploit block.  
    * Wrote a series of data pre-processing, standardization, and cross validation scripts that were used in order to compare state variables computed from a combination of event processing logic, on-chain function calls, and Token Flow  queries. This provided us with a high degree of confidence that the state at the time of Replant provided all users with the correct assets. 

## Role 

TBIQ will continue to build out Beanstalk Farms’ data engineering systems, analytics and visualization capabilities. TBIQ will focus on building on top of the subgraph infrastructure. Expected work includes:
* Setting up a hosted application that will serve as the Beanstalk data playground (this will serve a similar purpose to Dune, but will instead pull data from the subgraph and have custom visualizations);
* Building a variety of new visualizations for the data playground;
* Supporting the maintenance and building of the subgraph;
* Prototyping visualization ideas that could potentially be integrated into the Beanstalk UI; and
* Continuing to service high priority ad hoc data analysis and modeling requests from the rest of the Beanstalk Farms team as they surface.

## Payment 

9,000 Beans/month, paid twice per month, starting from Monday August 15, 2022 (conditional on Beanstalk Farms being funded by the DAO via future BIPs).

## Commitment

Part-time (average of 25 hr/wk).
