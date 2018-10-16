# Overview {#concept_p3s_qmj_bfb .concept}

Table Store is a distributed NoSQL data storage service that is built on Alibaba Cloud Apsara distributed system. It uses data partitioning and load balancing techniques to seamlessly scale up data size and access concurrency, providing storage of, and real-time access to, massive structured data.

Object Storage Service \(OSS\) is a massive-volume, secure, low-cost, and highly-reliable cloud storage service. It provides 99.999999999% data reliability. You can use RESTful API for storage and access in any place on the Internet. Its capacity and processing capability can be elastically scaled, and multiple storage modes are provided, comprehensively optimizing the storage cost.

## Scenarios {#section_mg2_t2c_dfb .section}

Table Store: Provides professional data-persistent storage service and user-oriented real-time read/write operations with high concurrency and low latency.

OSS: Supports backup at an extremely low cost.

## Usage {#section_atc_52c_dfb .section}

-   Write

    Data can be directly written to Table Store.

-   Read

    Data can be directly read from Table Store.

-   Back up

    Automatic backup is supported.

-   Restoration

    Data can be re-written to Table Store through Data Integration \(OSSReader and OTSWriter\).


## Constraints {#section_phf_v2c_dfb .section}

-   Write by whole row

    Table Store Stream requires that a whole row of data be written to Table Store each time. Currently, the whole-row data write mode is applied to the writing of time sequence data such as IoT data. Therefore, data cannot be modified subsequently.

-   Synchronization latency

    Currently, periodic scheduling is used and the scheduling interval is 5 minutes. The plugin has a latency of 5 minutes and the total latency of a synchronization task is 5 to 10 minutes.


## Activation {#section_ibs_w2c_dfb .section}

-   Activate Table Store
    1.  Go to the Table Store product details page.
    2.  Click **Buy Now**.
    3.  In the [Table Store console](https://partners-intl.aliyun.com/#/ots), create [instances](../../../../reseller.en-US/Quick Start/Create an instance.md#) and [data tables](../../../../reseller.en-US/Quick Start/Create a table.md#).

        **Note:** 

        -   To use the incremental tunnel, you must activate the [Stream function](../../../../reseller.en-US/Functions/Stream/Overview.md#) for data tables. You can select 24 hours for the validity period.
        -   Table Store supports the reserved CUs and additional CUs. If the reserved read and write CUs are both set to zero during table creation, then the additional read and write CU is used. You can adjust the reserved read/write CU of each table at any time.
        -   Table Store offers each registered account 25 GB of free storage per month.
-   Activate OSS
    1.  Go to the OSS product details page.
    2.  Click **Buy Now**.

## Data tunnel {#section_a1r_jfc_dfb .section}

Offline

-   Export the full data to OSS.

    -   [Script mode](reseller.en-US/Data channels/OSS/Full export (script mode).md#)
-   Synchronize data to OSS in incremental mode.

    -   [Script mode](reseller.en-US/Data channels/OSS/Incremental synchronization (script mode).md#)
-   Fully import data into Table Store.

    -   Script mode

