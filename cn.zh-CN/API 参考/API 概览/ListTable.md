# ListTable {#reference266 .reference}

获取当前实例下已创建的所有表的表名。

## 请求结构： { .section}

```language-pb
message ListTableRequest {
}

```

## 响应消息结构： { .section}

```language-pb
message ListTableResponse {
    repeated string table_names = 1;
}

```

table\_names:

-   类型：repeated string

-   当前实例下所有表的表名


**说明：** 若一个表刚刚创建成功，其表名会出现在 ListTableResponse 里，但此时该表不一定能够进行读写。

