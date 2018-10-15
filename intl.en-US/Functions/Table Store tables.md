# Table Store tables {#concept_27297_zh .concept}

When creating a Table Store table, you must specify a table name, a primary key, and reserved read/write throughput.

## Naming conventions {#section_wml_skg_cfb .section}

Table Store table names:

-   Can contain uppercase letters, lowercase letters, digits, and underscores.

-   Must start with an uppercase letter, an lowercase letter, or an underscore.

-   Are case sensitive.

-   Must be 1 to 255 characters in length.

-   Must be unique within the same instance \(tables in different instances are allowed to use the same name\).


## Primary Key { .section}

When creating a Table Store table, you must specify the primary key of the table. A primary key contains at least one, and up to four primary key columns. Each primary key column has a name and type. Table Store has some restrictions on the names and types of the primary key columns. For more information, see [Primary key and attribute](../../../../reseller.en-US/Data Models/Wide Column/Basic concepts/Primary key and attribute.md#).

Table Store indexes data based on the primary key. The primary key uniquely identifies each row in the table, so that no two rows have the same key. The rows are sorted in ascending order by their primary key.

## Reserved read/write throughput { .section}

To guarantee the consistent and low-latency performance of Table Store, you can specify the reserved read/write throughput during table creation. If the value of the reserved read/write throughput is not 0, Table Store reserves the necessary capacity to meet the specified throughput requirements. At the same time, costs are determined based on the reserved read/write throughput. You can dynamically raise and lower the reserved read/write throughput based on business requirements. The reserved read/write throughput is set in quantities of read capacity units and write capacity units.

**Note:** Tables created in capacity instances do not support the reserved read/write throughput.

You can update the tables reserved read/write throughput through the UpdateTable operation. The rules for updating the reserved read/write throughput are as follows.

-   A time interval of at least two minutes is required between two updates for the same table. For example, if you update the reserved read/write throughput of a table at 12:43:00, you must wait until after 12:45:00 to update the table for a second time. The required 2-minute time interval between updates is applied at the table level. Between 12:43:00 and 12:45:00, you can update the reserved read/write throughput for other tables.

-   The frequency of adjusting the reserved read/write throughput in a calendar day \(00:00:00 to 00:00:00 of the second day in UTC time\) is unlimited. The adjustment interval must be more than two minutes. Adjusting the reserved read/write throughput of a table is defined as adjusting either the read capacity unit or write capacity unit setting. Such an operation is considered as updating the table.

-   A reserved read/write throughput adjustment takes effect within one minute.


The consumed read/write throughput that exceeds the value of the reserved read/write throughput is classified as additional read/write throughput. Costs are calculated based on the unit price of the additional read/write throughput.

Initially, your applications may not have a high throughput. Depending on your business requirements, you can set a low reserved read/write throughput to minimize costs. As your business expands, you can increase the reserved read/write throughput of the table to reflect new business requirements. If you want to quickly import a large volume of data immediately after creating a table, you can set a high reserved write throughput to import the data quickly. After the large volume data import is completed, you can lower the reserved read/write throughput.

## Data size restrictions of partition key { .section}

Table Store partitions the table data according to the partition key ranges. Rows with the same partition key are placed in the same partition. To prevent large indivisible partitions, we recommend that the total data size for all rows under a single partition key value must not exceed 10 GB.

## Table Store load time {#section_uqt_zkg_cfb .section}

Table Store table is ready within one minute after it is created. You must wait for the table to finish loading before performing any data operations.

## Best Practice {#section_vqt_zkg_cfb .section}

 [See Table operations](../../../../reseller.en-US/Best Practices/Table operations.md#) 

## Table Store SDKs {#section_vmz_1lg_cfb .section}

[Use Table Store Java SDK for table operations](../../../../reseller.en-US/SDK Reference/Java SDK/Table-level operations.md#) 

 [Use Table Store Python SDK for table operations](../../../../reseller.en-US/SDK Reference/Python SDK/Preface.md#) 

