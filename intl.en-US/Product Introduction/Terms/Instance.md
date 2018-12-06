# Instance {#concept_hz2_btj_bfb .concept}

An instance is a logical entity in Table Store used to manage tables as a database in a relational database management system \(RDBMS\).

After activating Table Store, create an instance in the Table Store console and then create and manage tables within this instance. An instance is the basic unit in the resource management system of Table Store. Table Store implements access control and resource metering at the instance level.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20250/153957527111543_en-US.jpg)

You can create different instances for multiple businesses to manage their respective tables. You can also create multiple instances for one business based on different development, testing, and production purposes.

Table Store allows one Alibaba Cloud account to create up to 10 instances, and up to 64 tables can be created within each instance. If more instances or tables are needed, open a ticket.

## Naming rule {#section_whs_kvj_bfb .section}

The name of each instance is unique within each region. You can create instances of the same names across different service regions. Naming rule for each instance must:

-   Contain English letters, numbers, and hyphens\(-\)

-   Start with English letters

-   Not end with a hyphen\(-\)

-   Be case-insensitive

-   Be 3 Bytes to 16 Bytes in length

-   Not contain the words, such as ‘ali’, ‘ay’, ‘ots’, ‘taobao’,and ‘admin’


## Instance type {#section_b5f_mvj_bfb .section}

Table Store supports two instance types: high-performance instance and capacity instance.

**Note:** 

An instance type cannot be modified once the instance is created.

The two instance types have the same functions and support petabyte-sized data volumes for a single table, however, they differ in costs and scenarios.

-   High-performance instance

    High-performance instances support millions of read-write transactions per second \(TPS\) with 1 ms average latency of read and write operations per row. High-performance instances are suitable for scenarios requiring high read and write performance and concurrency, such as gaming, financial risk control, social networking applications, product recommendation systems, and public opinion sensing.

-   Capacity instance

    Capacity instances provide write throughput and write performance comparable to that of the high-performance instances, but with lower costs. However, the capacity instances do not equal the read performance and concurrency of high-performance instances. The capacity instances are suitable for services with high write frequency but low read frequency, and services with high affordability and reduced performance requirements. This includes access to log monitoring data, Internet of Vehicles data, device data, time sequence data, and logistics data.

    **Note:** 

    Capacity instances do not support reserved read/write throughput. All reads and writes are billed based on the additional read/write throughput.


## Instance type supported by region {#section_zjd_5vj_bfb .section}

|Region Name|High-performance instance|Capacity instance|
|:----------|:------------------------|:----------------|
|China East 1 \(Hangzhou\)|Support|Support|
|China East 2 \(Shanghai\)|Support|Support|
|China North 2 \(Beijing\)|Support|Support|
|China North 3 \(Zhangjiakou\)|In development|Support|
|China North 5 \(Huhehaote\)|In development|Support|
|China South 1 \(Shenzhen\)|Support|Support|
|Hong Kong|In development|Support|
|Singapore|Support|In development|
|US East 1 \(Virginia\)|Support|In development|
|US West 1 \(Silicon Valley\)|Support|In development|
|Asia Pacific NE 1 \(Japan\)|In development|Support|
|Germany 1 \(Frankfurt\)|In development|Support|
|Middle East 1 \(Dubai\)|In development|Support|
|Asia Pacific SE 2 \(Sydney\)|In development|Support|
|Asia Pacific SE 3 \(Kuala Lumpur\)|In development|Support|
|Asia Pacific SE 5 \(Jakarta\)|In development|Support|
|Asia Pacific SOU 1 \(Mumbai\)|In development|Support|

