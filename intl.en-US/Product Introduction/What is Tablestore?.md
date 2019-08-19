# What is Tablestore? {#concept_27280_zh .concept}

Tablestore is a NoSQL database service built on Alibaba Cloud’s Apsara distributed operating system that can store and access large volumes of structured data in real time , which allows you to:

-   Organize data into instances and tables that can seamlessly scale using data partitioning and load balancing.

-   Protect applications from faults and errors that may occur on the underlying hardware platform, providing fast recovery capability and high service availability.

-   Manage data with multiple backups using solid state disks \(SSDs\), enabling quick data access and high data reliability.


## Basic concepts {#section_prc_w53_bfb .section}

-   Data model

    The [data model](../../../../intl.en-US/Developer Guide/Wide column model/Introduction.md#) of Tablestore is defined by Table, Row, Primary Key, and Attribute. A table is a set of rows, and a row consists of the [primary key and attribute](../../../../intl.en-US/Developer Guide/Wide column model/Primary keys and attributes.md#).

-   Time To Live

    [Time To Live](../../../../intl.en-US/Developer Guide/Wide column model/Data versions and time to live.md#) \(TTL\) is a data table attribute measured in seconds that indicates the validity period of data. To save data storage space and reduce storage costs, Tablestore automatically clears any data that exceeds TTL.

-   Region

    Region refers to a service region of Alibaba Cloud. Tablestore is deployed across many service regions. You can select a suitable region tailored to your needs. For more information, see [Tablestore regions](intl.en-US/Developer Guide/Terms/Region.md#).

-   Read/write throughput

    The [read/write throughput](../../../../intl.en-US/Developer Guide/Terms/Read__write throughput.md#) is measured by read/write capacity units \(CUs\), which is the smallest billing unit for the data read and write operations. Read/write throughput includes Reserved throughput and Additional throughput.


## Related services {#section_lfq_y53_bfb .section}

After you load your data to Tablestore, you can use it with other Alibaba Cloud products and services.

-   For more information about how to export full data and synchronize incremental data in Tablestore to OSS, see [Overview](../../../../intl.en-US/Data channels/OSS/Overview.md#).

-   For more information about how to establish a seamless connection between Tablestore and MaxCompute under an Alibaba Cloud account, see [Allow MaxCompute to access Tablestore using one account](../../../../intl.en-US/Compute-Analysis/MaxCompute /Allow MaxCompute to access Table Store using one account.md#).

-   For more information about how to use Function Compute to perform real-time computation on incremental data in Tablestore data tables, see [Introduction to Function Compute](../../../../intl.en-US/Compute-Analysis/Function trigger/Introduction to Function Compute.md#).


## Usage {#section_lnf_dv3_bfb .section}

When using Tablestore, you only pay for the resources you reserve and use. Services including cluster resizing, upgrades, and maintenance of database software and hardware are managed free of charge.

Alibaba Cloud provides an intuitive operation interface for you to manage your Tablestore resources. You can log on to the [Tablestore console](https://ots.console.aliyun.com) to operate your instances.

You can also use APIs and SDKs to manage your Tablestore resources. For more information, see [Tablestore API Reference](../../../../intl.en-US/API Reference/Operations/OperationsSummary.md#) and [Tablestore SDK Reference](../../../../intl.en-US/SDK Reference/SDK overview.md).

## Pricing {#section_gvx_2v3_bfb .section}

Billing items of Tablestore include Data storage, Reserved read/write throughput, Additional read/write throughput and Downstream Internet traffic. For more information, see [Billing items and pricing](../../../../intl.en-US/Pricing/Billing items and pricing.md#).

For more information about Tablestore pricing, see [Tablestore Pricing page](https://www.alibabacloud.com/product/table-store/pricing).

