# DeleteIndex {#concept_htv_3ht_4fb .concept}

在指定的主表上删除索引表。

## 请求结构： {#section_opr_kgt_4fb .section}

```
message DropIndexRequest {
    required string main_table_name = 1;
    required string index_name = 2;
}
```

**main\_table\_name:**

-   类型：string
-   是否必要参数：是
-   要删除的索引表所在的主表

**index\_name:**

-   类型：string
-   是否必要参数：是
-   要删除的索引表名称

## 响应消息结构 {#section_wp4_ygt_4fb .section}

```

message DropIndexResponse {
}
```

