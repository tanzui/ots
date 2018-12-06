# DeleteTable {#reference242 .reference}

删除本实例下指定的表。

## 请求结构： { .section}

```language-pb
message DeleteTableRequest {
    required string table_name = 1;
}

```

table\_name:

-   类型: string

-   是否必要参数：是

-   需要删除的表的表名


## 响应消息结构： { .section}

```language-pb
message DeleteTableResponse {
}

```

DeleteTable 的响应中没有任何错误即表示表已经成功删除。

