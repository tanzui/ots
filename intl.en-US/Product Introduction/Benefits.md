# Benefits {#concept_27282_zh .concept}

Table Store is a fully managed NoSQL database service that provides fast and predictable performance with seamless scalability. Table Store lets you offload the administrative burdens of operating and scaling a distributed database, so that you don't have to worry about hardware provisioning, setup and configuration, replication, software patching, or cluster scaling. In addition, Table Store provides the following benefits:

|Advantage item|Description|
|:-------------|:----------|
|Scalability| -   Dynamic adjustment of reserved read/write throughput

When creating a table, you can configure the reserved read/write throughput for an application based on business requirements and data access conditions. Table Store schedules and reserves resources, based on the table’s reserved read/write throughput, to minimize the resource usage costs. You can dynamically adjust the table’s reserved read/write throughput based on the application.

-   Unlimited capacity

The amount of data stored in Table Store tables is unlimited. If a table size increases, Table Store adjusts the data partitions for immediate storage space allocation to the resized table.


 |
|Data reliability|Table Store stores multiple data copies across different servers in different racks. If a failure occurs, backup servers with copied data immediately restore services, resulting in zero data loss.|
|High availability|Through automatic failure detection and data migration, Table Store protects applications from both hardware and network-related faults to deliver high availability.|
|Ease of management|Table Store automatically manages complex tasks, such as the management of data partitions, software upgrades, hardware upgrades, configuration updates, and cluster resizing, allowing you to focus on growing your business.|
|Secure access platform|Table Store performs identity authentication for each application request, preventing unauthorized data access and ensuring data access security.|
|Strong consistency|Table Store guarantees strong consistency of writing data, and makes sure that the data 3 copies are written to disk and that the data will eventually be consistent across all storage locations. Once a successful result is returned for a write operation, applications can read the latest data.|
|Flexible data models|Table Store tables do not require a fixed format. The column numbers of each row, and the value types in columns of the same name but different rows, can be varied. Table Store supports multiple data types, such as Integer, Boolean, Double, String, and Binary.|
|Multi-tenancy mechanism|Table Store uses a shared storage mechanism, which allows multiple instances of different users to share the same cluster resource. Table Store uses the data partition as the smallest unit , which supports the load balancing mechanism at the data partition level to isolate the impact between different instances.|
|Pay-As-You-Go|Table Store only charges fees based on the actual resources you have reserved and used.|
|Monitoring integration|The Table Store console provides real-time monitoring information, including the request number per second and the average response latency.|

