# Data Operations {#concept_27357_zh .concept}

This article provides recommendations for optimizing Table Store data operations. Notably, it details how to effectively manage the attribute columns and application request errors.

## Split tables among attribute columns {#section_zx1_s34_dfb .section}

If a table’s rows have many attribute columns, but each operation only accesses a portion of these columns, you can split the table into multiple tables. The attribute columns of different access frequencies can be placed into different tables.

For example, in a merchandise management system with rows containing the item quantity, item price, and item description:

-   Item quantities and prices are integer-type values that consume little storage space, but are modified frequently.

-   Item descriptions are string-type values that consume more storage space, but are modified infrequently.


Because the majority of operations only require updating the integer-type values of item quantities and prices, the table can be split into two tables, one containing these two values, the other containing the string-type item descriptions.

## Compress text-based attribute columns { .section}

If an attribute column contains a large amount of text, the attribute columns can be compressed and stored as binary-type in Table Store. This process saves space and reduces the capacity units consumed by access operations, to reduce the cost of Table Store usage.

## Store attribute columns in OSS { .section}

Table Store limits the size of a single attribute column to 2 MB. If you need to store a file that exceeds 2 MB, we recommend that you use Alibaba Cloud Object Storage Service \(OSS\). OSS is an alternative storage service capable of storing large files at lower costs compared to Alibaba Cloud Table Store.

If OSS cannot be used, the attribute column whose value is greater than 2 MB can be split into multiple, smaller rows, and then stored in Table Store.

## Add error retry intervals { .section}

If an application’s request fails and returns a try again error, we recommend that you wait a period of time before trying the request again. As a best practice, randomized or exponentially-increasing backoffs are helpful to avoid an avalanche effect.

