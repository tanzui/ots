# Test indicators {#concept_64992_zh .concept}

Because all the stress tests use synchronous requests, the request QPS is correlated with the number of runners and the average latency. This relationship is as follows:

Total QPS = 1000/Average latency \* 5 \(number of runners\) \* Number of threads per runner

-   Runner: quantity of runners
-   Thread: number of YCSB threads created by each runner
-   QPS \(row\): number of rows written or read per second
-   Avg Latency \(ms\): average latency calculated by runners, in milliseconds

For example, in a scenario with four partitions, five runners, five threads per runner, and an average latency of 4 ms, the theoretical QPS for a single thread would be `1000/4 = 250`. The total QPS in this scenario would be `250 * 5 (number of runners) * 5 (number of threads per runner) = 6250`.

**Note:** This performance test is not a limit test of the service performance. The test does not trigger throttling measures on the Table Store server. The automatic load balancing feature of Table Store guarantees horizontal scale-up of the service capabilities provided by a single table.

