# What are the differences between Tablestore and spreadsheets in tables, rows, columns, and values?

Tablestore tables store structured data. You can query, insert, modify, and delete data in the tables. One user can create multiple tables. Data in the tables is organized in forms of columns, rows, and values.

![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/38581/cn_zh/1512539538965/38581-01.png)

The preceding figure compares concepts such as table between Tablestore and spreadsheets.

-   **table**: similar to sheets in spreadsheets. Each sheet corresponds to a table.
-   **row**: similar to rows in spreadsheets. Each row consists of column names and values in the columns.
-   **column**: similar to the column in spreadsheets. All data in a column shares the same dimension attributes.
-   **value**: similar to the cell in spreadsheets. Each value indicates the value of a column in a specific row. Unlike spreadsheets, Tablestore can contain columns that contain null values. The following data types are supported for values: STRING, INTEGER, BOOLEAN, DOUBLE, and BINARY. Data types of values in primary key columns can only be STRING, INTEGER, or BINARY.

