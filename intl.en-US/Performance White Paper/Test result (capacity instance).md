# Test result \(capacity instance\) {#concept_66213_zh .concept}

Test analysis

-   Given the same shard, if the change in latency for random write operations is insignificant as the stress increases, the resources in this shard are relatively adequate. As the stress increases, the QPS becomes greater.

-   Given the same shard, if the latency for random write operations becomes longer as stress increases, a queue of requests appears on the server where the shard is. As the stress increases, the QPS does not linearly increase.

-   The performance of the random read and random get range test cases is significantly affected by the cache hit rate. To prevent the cache hit rate from affecting the test results, we set the hit rate to an extremely low level. In the use of capacity instances, the actual performance quite likely exceeds the results of this test.

-   This performance test is not a limit test of the service performance. The test does not trigger throttling measures on the Table Store server. The automatic load balancing feature of Table Store guarantees horizontal scale-up of the service capabilities provided by a single table.


Random write performance

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20498/153976685112033_en-US.png)

Random read performance

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20498/153976685112034_en-US.png)

Random batch write performance

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20498/153976685112035_en-US.png)

Random get range performance

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20498/153976685112036_en-US.png)

