# Limits {#concept_plt_cpl_2fb .concept}

The following table describes the restrictions for Table Store. Some of the restrictions indicate the maximum values that can be used rather than the suggested values. In order to ensure better performance, please set the table structure and data size in a single row appropriately.

|Item|Limit|Description|
|:---|:----|:----------|
|Number of instances created under an Alibaba Cloud user account|10| To increase the limit, [open a ticket](https://workorder-intl.console.aliyun.com/#/ticket/createInd).

 |
|Number of tables in an instance|64| To increase the limit, [open a ticket](https://workorder-intl.console.aliyun.com/#/ticket/createInd).

 |
|Instance name length|3-16 Bytes|Can contain uppercase and lowercase letters, digits, and hyphens. Must begin with a letter, and must not end with a hyphen.

 Must not contain the words, such as ‘ali’, ‘ay’, ‘ots’, ‘taobao’and ‘admin’.

 |
|Table name length|1-255 Bytes|Can contain uppercase and lowercase letters, digits, and underscores. Must begin with a letter or underscore.

 |
|Column name length|1-255 Bytes|Can contain uppercase and lowercase letters, digits, and underscores. Must begin with a letter or underscore.

 |
|Number of primary key columns|1-4 columns|Must be at least one column.|
|Size of string type primary key column values|1 KB|A single primary key column’s string type column value is limited to 1 KB.|
|Size of string type attribute column values|2 MB|A single attribute column’s string type column value is limited to 2 MB.|
|Size of binary type primary key column values|1 KB|A single primary key column’s binary type column value is limited to 1 KB.|
|Size of binary type attribute column values|2 MB|A single attribute column’s binary type column value is limited to 2 MB.|
|Number of attribute columns in a single row|Unlimited|A single row can contain an unlimited amount of attribute columns.|
|The number of attribute columns written by one request|1024 columns|The number of attribute columns written by one PutRow, UpdateRow, or BatchWriteRow request in a single row.|
|Data size of a single row|Unlimited|The total size of all column names, and column value data, for a single row is unlimited.|
|Reserved read/write throughput for a single table|0-5000| To increase the limit, [open a ticket](https://workorder-intl.console.aliyun.com/#/ticket/createInd).

 |
|Number of columns in a read request’s columns\_to\_get parameter|0-128|The maximum number of columns obtained in a row of data in the read request.|
|Table-level operation QPS|10|The QPS of a table-level operation on an instance must not exceed 10.|
|Number of UpdateTable operations for a single table|increase: Unlimited Lower: Unlimited|The reserved read/write throughput for each table can be increased or lowered unlimited times within a calendar day \(from 00:00:00 to 00:00:00 of the next day in UTC time\).|
|UpdateTable frequency for a single table|Maximum of one update every 2 minutes|The reserved read/write throughput for a single table cannot be adjusted beyond the frequency of once every 2 minutes.|
|The number of rows read by one BatchGetRow request|100|N/A|
|The number of rows written by one BatchWriteRow request|200|N/A|
|Data size of one BatchWriteRow request|4 MB|N/A|
|Data returned by one GetRange operation|5,000 rows or 4 MB|The data returned by a single operation cannot exceed 5000 rows or 4 MB. Otherwise, the excessive data will be read with a returned token.|
|The data size of an HTTP Request Body|5 MB|N/A|

