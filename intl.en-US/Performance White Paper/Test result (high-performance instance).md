# Test result \(high-performance instance\) {#concept_64993_zh .concept}

Test analysis

-   Given the same shard, if the change in latency for random write operations is insignificant as the stress increases, the resources in this shard are relatively adequate. As the stress increases, the QPS becomes greater.

-   Given the same shard, if the latency for random write operations becomes longer as stress increases, a queue of requests appears on the server where the shard is. As the stress increases, the QPS may not linearly increase.

-   This performance test is not a limit test of the service performance. The test does not trigger throttling measures on the Table Store server. The automatic load balancing feature of Table Store guarantees horizontal scale-up of the service capabilities provided by a single table.


Random write performance

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20497/153976680012029_en-US.png)

Random read performance

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20497/153976680112030_en-US.png)

Random batch write performance![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20497/153976680112031_en-US.png)

Random get range performance

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20497/153976680112032_en-US.png)

