# TableMeta {#reference284 .reference}

表示一个表的结构信息。

## 数据结构 { .section}

```language-xml
message TableMeta {
    required string table_name = 1;
    repeated PrimaryKeySchema primary_key = 2;
}

```

table\_name:

-   类型：string

-   描述：该表的表名。


primary\_key:

-   类型：repeated [PrimaryKeySchema](intl.zh-CN/API 参考/DataType/PrimaryKeySchema.md#) 

-   描述：该表全部的主键列。


## 相关操作 { .section}

 [CreateTable](intl.zh-CN/API 参考/API 概览/CreateTable.md#)

 [DescribeTable](intl.zh-CN/API 参考/API 概览/DescribeTable.md#) 

