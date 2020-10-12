# Why do 5xx-related errors occur when I use Tablestore?

Some users may encounter 5xx-related errors when they use Tablestore. The following table lists the HTTP status codes.

|HTTP status code|Error code|Error message|
|----------------|----------|-------------|
|503|OTSPartitionUnavailable|The partition is not available.|
|503|OTSServerUnavailable|Server is not available.|
|503|OTSServerBusy|Server is busy.|
|503|OTSTimeout|Operation timeout.|

The possible cause is that Tablestore is a distributed NoSQL service. The server implements load balancing based on the amount of and access to data in each partition. This way, data and access can be seamlessly scaled and distributed to multiple servers.

The following figure shows that Tablestore distributes data to different partitions base on the order of the first primary key column. Different partitions are distributed to different racks across servers to provide data reading and writing services.

![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/59899/cn_zh/1505891625955/2017-09-18-18-14-45-00_00_06-00_00_11.gif)

The dynamic load balancing mechanism of Tablestore can detect whether a partition such as P1 in the following figure stores large amounts of data or processes too frequent access. In this case, the partition is split into two partitions P1 and P5. The two partitions are distributed to the node that supports lower loads.

**Tablestore uses the preceding automatic load balancing mechanism to store large amounts of data, process highly concurrent access, and implements automatic scalability without human intervention during the whole process.** Only one partition is created when a table is created, which provides limited concurrent read and write capabilities. The automatic load balancing mechanism delivers latencies. To address these issues, contact Alibaba Cloud engineers to split a table into multiple partitions in advance.

![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/59899/cn_zh/1505891569697/2017-09-18-18-04-42-00_00_12.80-00_00_26.gif)

Tablestore uses a shared storage mechanism. Partitions are the logical storage unit of Tablestore. When Tablestore implements load balancing, data is not migrated and only metadata of tables is modified. When the metadata changes, partitions may become unavailable for a short period of time to ensure data consistency. **In most cases, this period ranges from several hundred milliseconds to several seconds when the partitions are under heavy load.** The preceding error may occur when read and write operations are performed on the partitions during this period of time. If this error occurs, retry the operations. Tablestore SDK provides default retry policies. You can specify retry policies when you initialize an OTSClient instance.

Tablestore uses the API that complies with the standard Restful protocol. Due to the uncontrollability of the network environment, we recommend that you add a retry policy for all reading and writing operations to respond to network errors for fault tolerance.

**Note:** Data read by calling BatchWriteRow and BatchGetRow may be distributed to a thousand tables or multiple partitions of a table. A partition may be being split when operations are called on the table. Therefore, the operations as a whole is not atomic. To ensure that each single-row operation is atomic, check getFailedRows\(\) in the response for failed single-row operations when HTTP status code 200 is returned.

