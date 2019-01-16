# Data storage {#concept_okx_2tj_bfb .concept}

The total data volume of an instance is the sum of data from all tables in that instance. The total data volume of a table is the sum of data in all rows of that table. The total data volume of all rows is the sum of data in all primary key columns and attribute columns of a single row.

Data storage fees are based on the total volume of instance data. Fees are calculated on a per hour basis. Due to fluctuations in the total data volume utilized, Table Store collects the total data volume of all table partitions at regular intervals and calculates the average hourly total data volume. This average volume value is then multiplied by the unit price to account for the actual storage fee. For the latest unit price information, see [Table Store pricing details](https://www.alibabacloud.com/product/table-store/pricing).

The following examples illustrate how to calculate a row and table’s data volume.

## Calculation of a row’s data volume {#section_lpp_222_cfb .section}

The data in each row of a table consumes space in Table Store. When the Max Versions or [TTL](../../../../../intl.en-US/Data Models/Wide Column/Basic concepts/Data versions and Time To Live.md#) feature is enabled, the data of each version includes the [Version number](../../../../../intl.en-US/Data Models/Wide Column/Basic concepts/Data versions and Time To Live.md#) \(eight bits\), column name, and data value.

Storage space is calculated as follows:

-   Data size of a single row = size of the [primary keys](../../../../../intl.en-US/Data Models/Wide Column/Basic concepts/Primary key and attribute.md#) data + size of all [attribute columns](../../../../../intl.en-US/Data Models/Wide Column/Basic concepts/Primary key and attribute.md#) data.

Among of which,

-   Data size of a primary key = name length of the primary key column + size of the value of the primary key column

-   For more information about how to calculate the amount of data in an attribute column, see the detailed sample calculation of the amount of data for rows and tables in this document.


Data size of the column values is calculated as follows:

|Value type|Data size|
|:---------|:--------|
|String|Bytes of the string in UTF-8 encoding. If the string is null \(Table Store supports the null string type\), the data size is 0.|
|Interger|8 Bytes|
|Double|8 Bytes.|
|Boolean|1 Byte.|
|Binary|Bytes of the Binary data.|

An example of how to calculate a row’s data size is as follows:

ID \(Integer\) is the primary key column of the table. Where ID is a primary key column, and the others are attribute columns.

|ID|Name|Length|Comments|
|:-|:---|:-----|:-------|
|1|timestamp = 1466676354000, value = ‘zhangsan’|timestamp = 1466676354000, value = 20| timestamp = 1466676354000, value = String \(100 Bytes\)

 timestamp = 1466679954000, value = String \(150 Bytes\)

 |

The preceding table has two valid versions for attribute column "Comments".

-   If Max Versions = 2 and TTL = 2592000

    Data size of a single attribute column = \(Name length of the attribute column + 8\) \* Number of the valid versions + Total size of the values of all the valid versions in the attribute column

    **Note:** When the Max Versions or TTL feature is enabled \([Max versions](../../../../../intl.en-US/Data Models/Wide Column/Basic concepts/Data versions and Time To Live.md#) \> 1\) or TTL! = -1\), each version number consumes 8 Bytes of the storage space:

    The row’s data size is 334 bytes, and the calculation is as follows:

    -   Data size of the primary key = len \(‘ID’\) + len \(1\) = 10 Bytes

    -   Data size of the attribute column Name = \[len \(‘Name’\) + 8\]\*1 + len \(‘zhangsan’\) = 20 Bytes

    -   Data size of the attribute column Length = \[len \(‘Length’\) + 8\]\*1 + len \(20\) = 22 Bytes

    -   Data size of the attribute column Comments = \[len \(‘Comments’\] + 8\)\*2 + 100 + 150 = 282 Bytes

-   If Max Versions = 1 and TTL = -1,

    Data size of a single attribute column = Name length of the attribute column + Size of the value of the attribute column

    **Note:** 

    -   When the multiple versions \(that is, Max versions = 1\) or TTL \(that is, TTL = -1\) is disabled, the version number consumes no bytes of the storage space.
    -   Although the column Comments has two versions, as a result of Max Versions = 1, only the latest version is valid.
    The row’s data size is 194 bytes, and the calculation is as follows:

    -   Data size of the primary key = len \(‘ID’\) + len \(1\) = 10 Bytes

    -   Data size of the attribute column Name = len \(‘Name’\) + len \(‘jonathon’\) = 12 Bytes

    -   Data size of the attribute column Length = len \(‘Length’\) + len \(20\) = 14 Bytes

    -   Data size of the attribute column Comments = len \(‘Comments’\) + 150 \(Bytes\) = 158 Bytes


## Calculation of the table’s data size {#section_ptc_gg2_cfb .section}

Assume that there is a table whose primary key is ID \(Integer\), and other columns are attribute columns. If its Max Versions = 2 and TTL = –1, the table’s data size is calculated as follows:

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20254/154763140411617_en-US.png)

-   The data size of the row whose ID is 1 = 10 \(primary key size\) + \(116 + 166\) \(total data size of the two versions in the attribute column Comments\) = 292 Bytes

-   The data size of the row whose ID is 2 = 10 \(primary key size\) + 216 \(data size of a version in the attribute column Comments\) + 22 \(data size of a version in the attribute column Length\) = 248 Bytes

-   The total data volume of the table = 292 + 248 = 540 Bytes.


If the table's data volume does not change within an hour, the table is billed for 540 bytes. Table Store does not limit the data volume for an individual table and does not charge for unused resources.

**Note:** Before calculating the data size of the partition, Table Store asynchronously clears expired data from each partition and version data that exceeds the value of Max Versions. The time it takes to clear the data depends on the total data volume being cleared. This process is typically finished within 24 hours. The data written to a partition after a clear data operation is added to the partition’s data volume. This data is added upon completion of the next clearance operation.

