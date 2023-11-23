---
layout: default
title: Pilot real-world connection
parent: TAT Mint by Asset-Class
grand_parent: Assets Production
nav_order: 1
---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}



This is applicable for all quantifiable real-world assets to be connected, where $TAT records their respective market value of verifiable asset production.

# Production Audit

Production audit refers to the comparison of Producer’s reported production quantity against published government production record, from the previous month, to calculate a production deviation ratio.

According to the production deviation ratio, the Producer's collateral will be deducted if applicable, and the reported production quantity will be adjusted to align with the result of the production audit.

## How is a production audit conducted?

1. Each asset class has its own asset data module for data processing and audit. Here, the OilData and GasData modules are utilized, in tandem with the Oracle module.

2. Producers can use an automated Tool, the ProductionData Uploader for reporting each natural gas and oil well’s daily production.

Notice: Treasurenet automated Tool requires connection to industry accepted production recording hardware and data service.

3. Monthly, an automated Tool, the Oracle Feeder Tool, will fetch the production data of that well from the whitelisted government publications;

4. The OilData and GasData module will compare the reported values and published values to calculate the production deviation ratio for the month;

`Production deviation ratio = (Producer's reported production stored on the chain - whitelist reported production) / whitelist reported production \_ 100%`

5. Calculate the verified production quantity and its corresponding market value based on the production deviation ratio for the month;

6. Deduct collateral, if applicable, according to the production deviation ratio

## How to start a production audit?

1. In the WellManagement page of the Producer Portal, Producers select and click [Mint TAT].

2. Choose [yes] in the pop-up window

- Trigger the audit action for that month's production.
- Mint the corresponding TAT according to the production audit result


# Market Value

## What is market value?

`$TAT` is intended to record the economic value of the underlying various asset production.

Here, market value refers to the value of natural gas and oil produced as observed in real-world trading markets. Treasurenet’s Oracle module uses the Oracle Feeder Tool to query for values from whitelisted data sources.

## How to calculate market value?

Different assets have different ways of calculating market value. But, the general framework would be to

1. observe a publicly acceptable financial value and
2. apply any relevant discount factor

Here, the current market value calculation is simple for natural gas and oil, due to the naturally observable qualities of the asset.

```
- OIL Market value (USD) = Daily production * Daily price * Oil price discount
- GAS Market value (USD) = Daily production * Daily price
```

## What is the Oil price discount?

The oil price discount is a unique rule for this asset class, different oil grades correspond to different values. Acidity, and API gravity of the Oil production matters. These three values will affect the size of the oil price discount for this well, specifically as follows:

```
- 90% discount ratio: API gravity > 31.10 && Acidity < 0.50%
- 85% discount ratio: API gravity > 31.10 && Acidity >= 0.50%
- 80% discount ratio: API gravity <= 31.10 && Acidity < 0.50%
- 75% discount ratio: API gravity <= 31.10 && Acidity >= 0.50%
```

# Data Requirements

Oracle Feeder Tool queries publicly available production data and market price data sets to allow relevant asset data modules to conduct production audit. Workflows for each asset class can expect regular updates and upgrades, pending DAO Governance proposals.

## Oil and Gas Production Data

Whitelisted natural gas and oil production data sources:

- https://www.petrinex.gov.ab.ca/publicdata
- https://www.rrc.texas.gov/resource-center/research/data-sets-available-for-download/#production-data-table

Whitelisted Producer reported sources:

- Publicly traded SCADA (Supervisory Control and Data Acquisition) SaaS platforms

## Oil and Gas Market Price Data

Whitelisted price source is to be from a multitude of observable prices and will vary based on asset production geographic location.

Example whitelist sources:

- https://www.eia.gov/dnav/ng/ng_pri_fut_s1_d.htm
- https://oilprice.com/commodity-price-charts?page=chart&sym=CLY00