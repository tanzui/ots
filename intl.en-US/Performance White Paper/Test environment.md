# Test environment {#concept_64989_zh .concept}

## Table Store instances {#section_twq_1j4_dfb .section}

This test uses two Table Store instances:

-   High-performance instance: in the China East 1 region
-   Capacity instance: in the China East 1 region

**Note:** 

-   The high-performance instance and capacity instance use different disk types. The former uses SSDs and the latter uses a combination of SSD and SATA disks. The high-performance instance provides better performance in reading data from the disks. The capacity instance costs a far lower unit price with the performance comparable to the high-performance instance in writing data to the disks. The drawback is its low disk data reading performance. The two instances provide similar performance when reading data from the cache.
-   As a Pay-As-You-Go product, Table Store bills you based on the resources you actually use per hour. Table Store supports extremely high read/write concurrency. A large-scale performance test may result in high fees. If you plan to perform a large-scale performance test, [open a ticket](https://selfservice.console.aliyun.com/ticket/createIndex) to get the test result at a reasonable cost.

## Stress runners { .section}

All tests are completed with the same group of runners.

-   Quantity: 5
-   Type: Alibaba Cloud ECS
-   Region: zone F, China East 1
-   Model: Shared computing
-   Configuration:
    -   CPUs: 8 cores
    -   Memory: 8 GB
    -   Instance type: I/O optimized
    -   Network type: Classic network
    -   Operating system: Ubuntu 16.04 64-bit

**Note:** Because the quality of public network environments cannot be guaranteed and Table Store operations are sensitive to network latency, we use ECS instances in the same region as the Table Store instances and a private Table Store address to avoid interference from unpredictable network factors.

