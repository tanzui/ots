# Billing example {#concept_qhh_ngf_cfb .concept}

## Background {#section_mlq_rgf_cfb .section}

A user in California activates Table Store and creates a high-performance instance. The table data on the instance has a consistent read volume of around 10,000 read queries per second \(QPS\) per day, and the accessed data does not exceed 4 KB \(equivalent to 1 CU\). The user wants to know how the table is calculated on a daily basis. The calculated daily billing amount of the table is detailed in the following example.

## Case analysis {#section_rr4_tgf_cfb .section}

**Note:** In the following example, the unit prices were the actual prices on August 1, 2018. For the latest Table Store unit prices, see Alibaba Cloud website pricing.

|Billing items|Unit price|
|:------------|:---------|
|Additional read throughput|USD 0.0030/10,000 CU|

The calculated daily bill:

10000 \* 86400/10000 \* 0.0030 = USD 259.20

**Note:** Additional read/write throughput is billed based on the sum of additional CUs consumed. The number of CUs consumed per day is 10000 x 86400 = 864 million CUs.

