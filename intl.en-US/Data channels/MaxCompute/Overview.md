# Overview {#concept_60935_zh .concept}

Table Store is a distributed NoSQL data storage service that is built on Alibaba Cloud Apsara distributed system. It uses data partitioning and load balancing techniques to seamlessly scale up data size and access concurrency, providing storage of, and real-time access to, massive structured data.

MaxCompute is a big data computing service that provides a fast and fully hosted PB-level data warehouse solution, allowing you to analyze and process massive data economically and efficiently.

## Scenarios {#section_azx_zzt_cfb .section}

Table Store: Provides professional data-persistent storage service and user-oriented real-time read/write operations with high concurrency and low latency.

MaxCompute: Provides computing services, which are generally used for cleaning, correcting, and calculating data.

## Activation { .section}

Activate Table Store

1.  Go to the Table Store details page.
2.  Click **Buy Now**.
3.  In the [Table Store console](https://partners-intl.aliyun.com/#/ots), create [instances](../../../../reseller.en-US/Quick Start/Create an instance.md#) and [tables](../../../../reseller.en-US/Quick Start/Create a table.md#).

    **Note:** 

    -   To use the incremental tunnel, you must activate the [Stream function](../../../../reseller.en-US/Functions/Stream/Overview.md#) for tables. You can select 24 hours for the validity period.
    -   Table Store supports the reserved CUs and additional CUs. If the reserved read and write CUs are both set to zero during table creation, then the additional read and write CU is used. You can adjust the reserved read/write CUs of each table at any time.
    -   Table Store offers each registered account 25 GB of free storage per month.

Activate MaxCompute

1.  Go to the MaxCompute details page.
2.  Click **Buy now**.

    **Note:** Two billing methods are available, which are prepayment by CU cost and Pay-As-You-Go.


## Data tunnel { .section}

-   Real-time

    Direct read and write

-   Offline

    -   Incremental synchronization to MaxCompute

        [Wizard mode](reseller.en-US/Data channels/MaxCompute/Incremental synchronization (wizard mode).md#)

    -   Full export to MaxCompute

        [Script Mode](reseller.en-US/Data channels/MaxCompute/Full export (script mode).md#)

    -   Full import to Table Store

        Script Mode


