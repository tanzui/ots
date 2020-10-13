# Overview

Tunnel Service is a centralized service that uses the Table Store API to allow you to consume full and incremental data. Tunnel Service provides tunnels that are used to export and consume data in the full, incremental, and differential modes. After you create a tunnel, you can use it to consume historical and incremental data that is exported from a specified table.

Table Store is applicable to scenarios such as metadata management, time series data monitoring, and message systems. These scenarios often make use of incremental or full and incremental data streams to trigger operations such as:

-   Data synchronization: synchronizes data to a cache, search engine, or data warehouse.
-   Event triggering: triggers Function Compute, sends a notification when data is consumed, or calls an API operation.
-   Stream data processing: connects to a stream-computing engine or a stream- and batch-computing engine.
-   Data migration: backs up data to OSS or migrates data to a Table Store capacity instance.

