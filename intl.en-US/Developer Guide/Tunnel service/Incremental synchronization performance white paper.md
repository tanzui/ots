# Incremental synchronization performance white paper {#concept_dd2_xdn_zgb .concept}

This topic describes the test on the performance of incremental synchronization through Tunnel Service, including the test environment, tools, plan, indicators, results, and summary.

## Test environment {#section_ngw_12n_zgb .section}

-   Table Store instance
    -   Type: high-performance instance
    -   Region: China \(Hangzhou\)
    -   Address: a private IP address, which prevents interference caused by unknown network issues.
-   Test server configuration
    -   Type: Alibaba Cloud ECS
    -   Region: China \(Hangzhou\)
    -   Model: ecs.mn4.4xlarge balanced entry-level model
    -   Configuration:
        -   CPU: 16 cores
        -   Memory: 64 GB
        -   NIC: VirtIO network device of Red Hat, Inc.
        -   Operating system: CentOS 7u2

## Test tools {#section_c5z_42n_zgb .section}

-   Stress testing tool

    The stress testing tool of Table Store is used to write data to multiple rows simultaneously by calling the BatchWriteRow operation through the Table Store Java SDK.

-   Pre-splitting tool

    The stress testing tool of Table Store is used to automatically create and pre-split tables based on the configured table names and the number of partitions.

-   Rate statistics tool

    The Table Store Java SDK can collect statistics of the consumption rate of incremental data and the total number of consumed rows in real time. You can add the logic demonstrated in the following example to the callback to collect rate statistics.

    **Example**

    ``` {#codeblock_0qi_dwz_mdi}
    private static final Gson GSON = new Gson();
        private static final int CAL_INTERVAL_MILLIS = 5000;
        static class PerfProcessor implements IChannelProcessor {
            private static final AtomicLong counter = new AtomicLong(0);
            private static final AtomicLong latestTs = new AtomicLong(0);
            private static final AtomicLong allCount = new AtomicLong(0);
    
            @Override
            public void process(ProcessRecordsInput input) {
                counter.addAndGet(input.getRecords().size());
                allCount.addAndGet(input.getRecords().size());
                if (System.currentTimeMillis() - latestTs.get() > CAL_INTERVAL_MILLIS) {
                    synchronized (PerfProcessor.class) {
                        if (System.currentTimeMillis() - latestTs.get() > CAL_INTERVAL_MILLIS) {
                            long seconds = TimeUnit.MILLISECONDS.toSeconds(System.currentTimeMillis() - latestTs.get());
                            PerfElement element = new PerfElement(System.currentTimeMillis(), counter.get() / seconds, allCount.get());
                            System.out.println(GSON.toJson(element));
                            counter.set(0);
                            latestTs.set(System.currentTimeMillis());
                        }
                    }
                }
            }
    
            @Override
            public void shutdown() {
                System.out.println("Mock shutdown");
            }
        }
    ```


## Test plan {#section_tcf_dfn_zgb .section}

When Tunnel Service is used for data synchronization, it synchronizes data sequentially within a single channel to maintain the order of data, and synchronizes data in different channels in parallel. For incremental data, the number of channels is equal to the number of partitions in a table. This performance test focuses on how the number of partitions \(channels\) affects the incremental synchronization rate because the overall performance of Tunnel Service is greatly correlated with the number of partitions.

-   Test scenarios

    The test is conducted in the following scenarios:

    -   Single-server single-partition synchronization
    -   Single-server 4-partition synchronization
    -   Single-server 8-partition synchronization
    -   Single-server 32-partition synchronization
    -   Single-server 64-partition synchronization
    -   Double-server 64-partition synchronization
    -   Double-server 128-partition synchronization
    **Note:** The test in the preceding scenarios is not an extreme test of the service performance, and therefore does not impose much pressure on the Table Store instance.

-   Test procedure
    1.  Create and pre-split a table for each test scenario.
    2.  Create a tunnel for incremental synchronization.
    3.  Use the stress testing tool to write incremental data.
    4.  Use the rate statistics tool to measure the QPS in real time, and check the consumption of system resources, such as CPU and memory.
    5.  Check the total bandwidth consumed during the incremental synchronization.
-   Test data description

    As shown in the following figure, sample data includes four primary key columns and one or two attribute columns. The size of each row is approximately 220 bytes. The first primary key \(partition key\) is a 4-byte hash value, which ensures that stress testing data is evenly written to each partition.

    ![](images/40034_en-US.png)


## Test indicators {#section_m3k_3gn_zgb .section}

This test uses the following indicators:

-   QPS \(row\): the number of rows synchronized per second.
-   Average latency \(ms per 1,000 rows\): the time required to synchronize 1,000 rows, in milliseconds.
-   CPU \(core\): the total number of single-core CPUs used for data synchronization.
-   Memory \(GB\): the total physical memory used for data synchronization.
-   Bandwidth \(Mbit/s\): the total bandwidth used for data synchronization.

**Note:** This performance test is based on user experience, rather than extreme testing.

## Test results {#section_a4p_rgn_zgb .section}

This section describes the test results for each scenario. For more information, see test details.

-   QPS and latency

    The following figure shows the number of rows synchronized per second and the time required to synchronize 1,000 rows in each scenario. In this figure, the QPS increases linearly with the number of partitions.

    In the single-server 64-partition synchronization scenario, the gigabit NIC works at its full capacity, resulting in only 570,000 QPS. For more information, see test details. The QPS in the double-server 64-partition synchronization scenario reaches 780,000, which is approximately twice the 420,000 QPS in the single-server 32-partition synchronization scenario. In the double-server 128-partition synchronization scenario, the QPS reaches 1,000,000.

    ![](images/40035_en-US.png)

-   System resource consumption

    The following figure shows the CPU and memory usage in each scenario. The CPU usage increases linearly with the number of partitions.

    The single-server single-partition synchronization uses 0.25 single-core CPUs. When the QPS reaches 1,000,000 in the double-server 128-partition synchronization scenario, only 10.2 single-core CPUs are used. The memory usage increases linearly with the number of partitions when it is less than 32. When more partitions, for example, 32, 64, or 128 partitions in this test, need to be processed, the memory usage is stably around 5.3 GB on each server.

    ![](images/40036_en-US.png)

-   Total bandwidth consumption

    The following figure shows the total bandwidth consumed during the incremental synchronization. In this figure, the consumed bandwidth increases linearly with the number of partitions.

    The single-server 64-partition synchronization uses a total bandwidth of 125 Mbit/s, which is the maximum rate supported by the gigabit NIC. In the double-server 64-partition synchronization scenario, the consumed bandwidth is 169 Mbit/s, which is the actual bandwidth required for 64-partition synchronization. This is approximately twice the 86 Mbit/s bandwidth required in the single-server 32-partition synchronization scenario. When the QPS reaches 1,000,000 in the double-server 128-partition synchronization scenario, the total bandwidth consumed reaches 220 Mbit/s.

    ![](images/40037_en-US.png)


## Test details {#section_rfg_33n_zgb .section}

-   Single-server single-channel: 19,000 QPS.
    -   Tested at: 17:40, January 30, 2019.
    -   QPS: steady at approximately 19,000 rows per second, with a peak rate of 21,800 rows per second.
    -   Latency: approximately 50 ms per 1,000 rows.

        ![](images/40038_en-US.png)

    -   CPU usage: approximately 25% of a single-core CPU.
    -   Memory usage: approximately 0.4% of the total physical memory, which is approximately 0.256 GB. \(Each test server provides 64 GB physical memory.\)
    -   Bandwidth consumption: approximately 4,000 Kbit/s.

        ![](images/40039_en-US.png)

-   Single-server 4-partition synchronization: 70,000 QPS.

    ![](images/40040_en-US.png)

    -   Tested at: 20:00, January 30, 2019.
    -   QPS: steady at approximately 70,000 rows per second, with a peak rate of 72,400 rows per second.
    -   Latency: approximately 14.28 ms per 1,000 rows.

        ![](images/40041_en-US.png)

    -   CPU usage: approximately 70% of a single-core CPU.
    -   Memory usage: approximately 1.9% of the total physical memory, which is approximately 1.1 GB. \(Each test server provides 64 GB physical memory.\)
    -   Bandwidth consumption: approximately 13 Mbit/s.

        ![](images/40042_en-US.png)

-   Single-server 8-partition synchronization: 130,000 QPS.

    ![](images/40043_en-US.png)

    -   Tested at: 20:20, January 30, 2019.
    -   QPS: steady at approximately 130,000 rows per second, with a peak rate of 141,644 rows per second.
    -   Latency: approximately 7.69 ms per 1,000 rows.

        ![](images/40044_en-US.png)

    -   CPU usage: approximately 120% of a single-core CPU.
    -   Memory usage: approximately 4.1% of the total physical memory, which is approximately 2.62 GB. \(Each test server provides 64 GB physical memory.\)
    -   Bandwidth consumption: approximately 27 Mbit/s.

        ![](images/40045_en-US.png)

-   Single-server 32-partition synchronization: 420,000 QPS.

    ![](images/40046_en-US.png)

    -   Tested at: 15:50, January 31, 2019.
    -   QPS: steady at approximately 420,000 rows per second, with a peak rate of 447,600 rows per second.
    -   Latency: 2.38 ms per 1,000 rows.

        ![](images/40047_en-US.png)

    -   CPU usage: approximately 450% of a single-core CPU.
    -   Memory usage: approximately 8.2% of the total physical memory, which is approximately 5.25 GB. \(Each test server provides 64 GB physical memory.\)
    -   Bandwidth consumption: approximately 86 Mbit/s.

        ![](images/40048_en-US.png)

-   Single-server 64-partition synchronization: 570,000 QPS, with the gigabit NIC working at its full capacity.

    ![](images/40049_en-US.png)

    -   Tested at: 22:10, January 31, 2019.
    -   QPS: steady at approximately 570,000 rows per second, with a peak rate of 581,400 rows per second.
    -   Latency: approximately 1.75 ms per 1,000 rows.

        ![](images/40050_en-US.png)

    -   CPU usage: approximately 640% of a single-core CPU.
    -   Memory usage: approximately 8.4% of the total physical memory, which is approximately 5.376 GB. \(Each test server provides 64 GB physical memory.\)
    -   Bandwidth consumption: approximately 125 Mbit/s, which is the maximum rate of the gigabit NIC.

        ![](images/40051_en-US.png)

-   Double-server 64-partition synchronization: 780,000 QPS.

    ![](images/40052_en-US.png)

    -   Tested at: 22:30, January 31, 2019.
    -   QPS: steady at approximately 390,000 rows per second on each server and 780,000 rows per second on both servers.
    -   Latency: approximately 1.28 ms per 1,000 rows.

        ![](images/40053_en-US.png)

    -   CPU usage: approximately 420% of a single-core CPU on each server and 840% of a single-core CPU on both servers.
    -   Memory usage: approximately 8.2% of the total physical memory, which is approximately 10.5 GB. \(Each test server provides 64 GB physical memory.\)
    -   Bandwidth consumption: approximately 169 Mbit/s. This indicates that bandwidth becomes the bottleneck when the number of partitions reaches 64 in single-server scenarios.

        ![](images/40054_en-US.png)

-   Double-server 128-partition synchronization: 1,000,000 QPS, with both gigabit NICs almost working at their full capacities.

    ![](images/40033_en-US.png)

    -   Tested at: 23:20, January 31, 2019.
    -   QPS: steady at approximately 500,000 rows per second on each server and 1,000,000 rows per second on both servers.
    -   Latency: approximately 1 ms per 1,000 rows.

        ![](images/40055_en-US.png)

    -   CPU usage: approximately 560% of a single-core CPU on each server and 1,020% of a single-core CPU on both servers.
    -   Memory usage: approximately 8.2% of the total physical memory, which is approximately 10.5 GB. \(Each test server provides 64 GB physical memory.\)
    -   Bandwidth consumption: approximately 220 Mbit/s.

        ![](images/40056_en-US.png)


## Summary {#section_yz5_13n_zgb .section}

Based on this performance test for incremental synchronization, the QPS for tables with a single or a few partitions is mainly affected by the latency in data reading and only few resources on the server are consumed. As the number of partitions increases, the overall throughput of incremental synchronization through Tunnel Service increases linearly until the system bottleneck, such as the bandwidth in this test, is encountered. When a resource on a single server is used up, this resource becomes the bottleneck. You can add more servers to increase the overall throughput. This test validates the excellent horizontal scaling performance of Tunnel Service.

