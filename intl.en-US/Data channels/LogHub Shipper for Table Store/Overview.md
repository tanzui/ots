# Overview {#concept_urh_1tt_lgb .concept}

LogHub Shipper for Table Store \(LogHub Shipper\) writes data from Log Service to a specified table in Table Store after data scrubbing and conversion. This service publishes data to the Alibaba Cloud Container Hub by using the Docker image method and runs on your ECS instances based on Container Service.

## Description {#section_mrg_xtt_lgb .section}

Log Service stores data in the JSON format, and writes to and reads from a [log group](../../../../intl.en-US/Product Introduction/Basic concepts/Overview.md) as the basic unit. Therefore, you cannot quickly search and analyze logs based on specific conditions in Log Service, for example, log data of an app for the last 12 hours.

LogHub Shipper converts log data in Log Service into structured data, and then writes the data to data tables in Table Store in real time. This provides an accurate and high-performance online service in real time.

## Example {#section_nrg_xtt_lgb .section}

For example, Log Service contains log data in the format as follows:

```
{"__time__":1453809242,"__topic__":"","__source__":"10.170.148.237","ip":"10.200.98.220","time":"26/Jan/2016:19:54:02 +0800","url":"POST /PutData? Category=YunOsAccountOpLog&AccessKeyId=U0U***45A&Date=Fri%2C%2028%20Jun%202013%2006%3A53%3A30%20GMT&Topic=raw&Signature=pD12XYLmGxKQ%2Bmkd6x7hAgQ7b1c%3D HTTP/1.1","status":"200","user-agent":"aliyun-sdk-java"}
```

When LogHub Shipper writes the data to a data table that contains the ip and time primary keys in Table Store, the data format is as follows.

|ip|time|source|status|user-agent|url|
|:-|:---|:-----|:-----|:---------|:--|
|10.200.98.220|26/Jan/2016:19:54:02 +0800|10.170.148.237|200|aliyun-sdk-java|POST /PutData…|

In this way, you can easily and accurately retrieve history data of a specified IP address based on a specified time period by using Table Store.

LogHub Shipper provides flexible data mapping rules. You can configure the mappings between the fields of log data and the attribute columns of data tables and easily convert the data.

## Concepts {#section_crr_bvt_lgb .section}

 **Related products** 

Before using LogHub Shipper, you should understand the following concepts:

-   Log Service
    -   [Endpoint, Project, Logstore, and Partition](https://www.alibabacloud.com/help/doc-detail/52895.htm?spm=a2c63.p38356.b99.1.3b9c6612RrUvIx)
    -   [Consumer group](../../../../intl.en-US/User Guide/Real-time subscription and consumption/Consumption by consumer groups/Consumer group - Usage.md) 

        We recommend that you use the same unique consumer group in LogHub Shipper for the same project, Logstore, and target table.

-   Table Store

    -   [Endpoint, Instance](../../../../intl.en-US/Product Introduction/Terms/Region.md)
    -   [Table, Primary key column, and Attribute column](../../../../intl.en-US/Data Models/Wide Column/Primary keys and attributes.md)
    -   [Throughput](../../../../intl.en-US/Data Models/Wide Column/Read__write throughput.md)
-   ECS instance

    -   [Pay-As-You-Go, Subscription](../../../../intl.en-US/Pricing/Pricing overview.md)
-   Container Service

    -   [Cluster, Node, Application, Service, and Container](../../../../intl.en-US/Product Introduction/Terms.md#) 

        We recommend that the number of containers in a single LogHub Shipper process be the same as or less than the number of partitions in the corresponding Logstore.

-   Access control

    -   [RAM user](../../../../intl.en-US/Product Introduction/What is RAM?.md)

        We recommend that you authorize the RAM user of LogHub Shipper to only read from Logstores and write to Table Store.


 **Data table** 

This is a target table that stores your log data after data scrubbing and conversion.

When using a data table, follow these rules:

-   You have to manually create a target table, because LogHub Shipper does not automatically create tables.

-   If Log Service and Table Store are both available, the latency between the time when a log entry enters Log Service and the time when the log entry goes to Table Store is measured in a few hundred milliseconds \(ms\).

-   When Table Store is unavailable, LogHub Shipper will wait for a period of 500 ms or less and try again.

-   LogHub Shipper regularly records persistent breakpoints.

-   If LogHub Shipper is unavailable, for example, during an upgrade, the service continues to consume logs from the last breakpoint upon recovery.

-   We recommend that different log entries in the same Logstore correspond to different rows in the target table. Therefore, any retries cannot affect the eventual consistency of the target table.

-   LogHub Shipper writes data by using the UpdateRow operation in Table Store. Therefore, multiple LogHub Shipper processes can share the same target table. In this situation, we recommend that LogHub Shipper write data to different attribute columns during these processes.


 **Status table** 

LogHub Shipper uses a status table that you create in Table Store to indicate some status.

When using a status table, follow these rules:

-   Multiple LogHub Shipper processes can share the same status table.
-   When no errors occur, each LogHub Shipper container adds a record to the status table at five-minute intervals.
-   When an error occurs but Table Store is still available, each LogHub Shipper container immediately adds a record to the status table.
-   We recommend that you set Time To Live \(TTL\) by days to only keep recent data.

The status table has four primary key columns:

-   project\_logstore: String type. This column indicates the project and Logstore of Log Service, separated with vertical bars \(|\).
-   shard: Integer type. This column indicates the shard number in Log Service.
-   target\_table: String type. This column indicates the name of the target table where you store data in Table Store.
-   timestamp: Integer type. This column indicates the time when a LogHub Shipper container adds a record to the status table. This is UNIX time, measured in milliseconds.

In addition, the following attribute columns record data import status. In any row in a status table, all attribute columns are optional and may not exist.

-   shipper\_id: String type. This column indicates the ID of a LogHub Shipper container. Currently, this is the name of the container host.
-   error\_code: String type. This column indicates an error code defined in Table Store. This attribute column does not exist if no error occurs.
-   error\_message: String type. This column indicates the specific error message that Table Store returns. This attribute column does not exist if no error occurs.
-   failed\_sample: String type. This column indicates an error log entry as a JSON string.
-   `__time__`: Integer type. This column indicates the maximum value that the specified LogHub Shipper container writes to the [\_time\_\_ field](../../../../intl.en-US/Product Introduction/Basic concepts/Overview.md) of log data in Table Store after this container last updates the status table.
-   row\_count: Integer type. This column indicates the number of log entries that the specified LogHub Shipper container writes to Table Store after this container last updates the status table.
-   cu\_count: Integer type. This column indicates the number of [Capacity Units \(CUs\)](../../../../intl.en-US/Data Models/Wide Column/Read__write throughput.md) that the specified LogHub Shipper container consumes after this container last updates the status table.
-   skip\_count: Integer type. This column indicates the number of log entries that the specified LogHub Shipper container cleans after this container last updates the status table.
-   \* skip\_sample: Str type. This column indicates one of the log entries, a JSON string, that the LogHub Shipper container discards after this container last updates the status table. The log of the container records each discarded log entry and the reason for discarding the log entry.

## Configuration {#section_dg1_1yt_lgb .section}

When you create LogHub Shipper, specify the following environment variables for the container:

-   access\_key\_id and access\_key\_secret: these are the AccessKeyId and AccessKeySecret of the Alibaba Cloud account that you use in LogHub Shipper.
-   loghub: this is the configuration of the Log Service instance that LogHub Shipper requires. This JSON object includes:
    -   endpoint
    -   logstore
    -   consumer\_group
-   tablestore: this is the configuration of the Table Store instance that LogHub Shipper requires. This JSON object includes:
    -   endpoint
    -   instance: the name of the instance.
    -   target\_table: the name of the data table. This table must exist under this instance.
    -   status\_table: the name of the status table. This table must exist under this instance.
-   exclusive\_columns: the blacklist of attribute columns. This is a JSON array that consists of JSON strings.

    If you have set this variable to a field, LogHub Shipper does not write the specified field to the target table. For example, the target table contains primary key A, the exclusive\_columns environment variable has \["B", "C"\] configured, and a log entry contains three fields: A, B, and D. Then, one row appears in the target table to indicate the log entry. This row contains primary key A and attribute column D. Column C does not exist in the log entry, so LogHub Shipper does not write this column to the target table. Column B exists in the log entry, but this column is specified as an exclusive column, so LogHub Shipper does not write this column to the target table either.

-   transform: indicates a simple conversion. This is a JSON object. The key in this variable is the name of the column that can be a primary key column written in the target table. The value is the simple conversion expression that LogHub Shipper defines as follows:
    -   A log field is an expression.
    -   An unsigned integer is an expression.
    -   A string in double quotes is an expression. The string can contain the escape characters `\"` and `\\\\`.
    -   `( func arg... )` is also an expression. Zero or multiple spaces or tabs can exist preceding and following the parentheses. At least one space exists between func and the parameter that follows func, and between different parameters. Each parameter must be an expression. The system supports the following functions:
        -   `->int`: converts a string to an integer. This function requires two parameters. The first is the base, which can be 2 to 36. The second is the string that LogHub Shipper converts. The letter in the second parameter is case insensitive and indicates a number from 10 to 35.
        -   `->bool`: converts a string to a Boolean value. This function requires one parameter that is the string LogHub Shipper converts. "true" corresponds to a true value and "false" corresponds to a true value. Other strings are regarded as errors.
        -   `crc32`: calculates CRC32 for a string and outputs the result as an Integer value. This function requires one parameter that is the string LogHub Shipper converts.

If a log entry is missing or an error occurs during conversion, the column corresponding to the key is regarded as a missing column. If an error occurs, the log of the container records error details.

Data scrubbing follows only one rule: if a primary key column is missing, LogHub Shipper cleans the corresponding log entry.

