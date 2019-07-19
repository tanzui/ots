# Overview {#concept_gmh_hs3_kgb .concept}

Tunnel Service is an integrated service for full and incremental data consumption based on Table Store API. It provides you with real-time consumption tunnels for distributed data, including incremental data, full data, and full and incremental data. By creating tunnels for a table, you can easily consume historical data and new data in the table.

## Background {#section_sgz_dmx_qnq .section}

Table Store is applicable to applications such as metadata management, time series data monitoring, and message systems. These applications often use incremental data streams or full and incremental data streams to trigger extra operations, including:

-   Data synchronization: synchronizes data to a cache, search engine, or data warehouse.
-   Event driving: triggers Function Compute, sends a consumption notification, or calls an API operation.
-   Stream data processing: connects to a stream-processing engine or a stream- and batch-processing engine.
-   Data migration: backs up data to OSS or migrates data to a Table Store capacity instance.

You can use Tunnel Service to easily build efficient and elastic solutions to consume full data, incremental data, and full and incremental data in the preceding scenarios.

## Features {#section_t7p_orf_2mg .section}

The following table lists the features provided by Tunnel Service.

|Feature|Description|
|-------|-----------|
|Tunnels for full and incremental data consumption| Tunnel Service allows you to consume incremental data, full data, and full and incremental data simultaneously.

 |
|Orderly incremental data consumption| Tunnel Service distributes incremental data to one or more logical partitions sequentially based on the write time. Data in different partitions can be consumed simultaneously.

 |
|Consumption latency monitoring| Tunnel Service allows you to call the DescribeTunnel operation to view the recovery point objective \(RPO\) information of the consumed data on each client. Tunnel Service also allows you to monitor data consumption of tunnels in the Table Store console.

 |
|Horizontal scaling of data consumption capabilities| Tunnel Service supports automatic load balancing among logical partitions to accelerate data consumption.

 |

