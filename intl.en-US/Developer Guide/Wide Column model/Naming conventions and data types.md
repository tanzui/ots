# Naming conventions and data types

This topic describes the naming conventions for tables and columns, as well as the data types supported by primary key columns and attribute columns.

## Naming conventions

The following table describes the naming conventions for tables and columns.

|Item|Description|
|:---|:----------|
|Structure|A name can contain uppercase letters \(A to Z\), lowercase letters \(a to z\), digits \(0 to 9\), and underscores \(\_\).|
|First character|A name must start with an uppercase letter \(A to Z\), a lowercase letter \(a to z\), or an underscore \(\_\).|
|Case sensitivity|A name is case-sensitive.|
|Length|A name can be 1 to 255 characters in length.|
|Uniqueness|-   A table name must be unique in the same instance.
-   Table names can be the same in different instances. |

## Data types

Data types supported by primary key columns include STRING, INTEGER, and BINARY. Data types supported by attribute columns include STRING, INTEGER, DOUBLE, BOOLEAN, and BINARY.

-   Data types supported by primary key columns

    |Data type|Definition|Size limit|
    |:--------|:---------|:---------|
    |String|Data is UTF-8 encoded. Empty strings are allowed.|Up to 1 KB|
    |Integer|Data is 64-bit integers. Auto-increment columns are supported.|8 Bytes|
    |Binary|Data is of the BINARY type. Empty values are allowed.|Up to 1 KB in length|

-   Data types supported by attribute columns

    |Data type|Definition|Size limit|
    |:--------|:---------|:---------|
    |String|Data is UTF-8 encoded. Empty strings are allowed.|For more information, see [Limits](/intl.en-US/Developer Guide/Limits.md).|
    |Integer|Data is 64-bit integers.|8 Bytes|
    |Double|Data is 64-bit and of the DOUBLE type.|8 Bytes|
    |Boolean|Data is of the BOOLEAN type. The value can be True or False.|1 Byte|
    |Binary|Data is of the BINARY type. Empty values are allowed.|For more information, see [Limits](/intl.en-US/Developer Guide/Limits.md).|


