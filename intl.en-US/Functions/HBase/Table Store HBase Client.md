# Table Store HBase Client {#concept_50125_zh .concept}

In addition to SDKs and RESTful APIs, Table Store HBase Client can be used to access Table Store through Java applications built on open source HBase APIs. Based on Java SDKs for Table Store version 4.2.x and later, Table Store HBase Client supports open source APIs for HBase version 1.x.x and later.

Table Store HBase Client can be obtained from any of the following three channels:

-   [GitHub tablestore-hbase-client project](https://github.com/aliyun/aliyun-tablestore-hbase-client) 
-    [Compressed package](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/50125/cn_zh/1486705375325/tablestore-hbase-client-1.2.0.zip) 
-   Maven

    ```language-xml
     <dependencies>
            <dependency>
                <groupId>com.aliyun.openservices</groupId>
                <artifactId>tablestore-hbase-client</artifactId>
                <version>1.2.0</version>
            </dependency>
        </dependencies>
    
    ```


Table Store is a fully managed NoSQL database service. When using TableStore HBase Client, you can simply ignore HBase Server. Instead, you only need to perform table or data operations using APIs exposed by Client.

Compared with self-built HBase services, Table Store has the following advantages:

|Items|Table Store|Self-built HBase cluster|
|:----|:----------|:-----------------------|
|Cost|Billing is based on actual data volumes. By providing high performance and capacity instances, Table Store can be tailored to all scenarios.|Allocates resources based on traffic peaks. Resources remain idle during off-peak periods, resulting in high operation and maintenance costs.|
|Security|Integrates Alibaba Cloud RAM and supports multiple authentication and authorization mechanisms, VPC, and primary/RAM user account management. Authorization granularity can be defined at both the table-level and API-level.|Requires extra security mechanisms.|
|Reliability|Supports automatic redundant data backup and failover. Data availability is 99.9% or greater, and data reliability is 99.99999999%.|Is dependent on cluster reliability.|
|Scalability|Server Load Balancer of Table Store supports PB-level data transfer from a single table. Manual resizing is not needed even if millions of bytes of data is concurrently stored.|Complex online/offline processes are required if a cluster reaches high usage capacity, which can severely impact online services.|

