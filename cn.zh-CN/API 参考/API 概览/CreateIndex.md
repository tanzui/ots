# CreateIndex {#concept_dlw_xft_4fb .concept}

在指定的主表上创建索引表。

## 请求结构： {#section_opr_kgt_4fb .section}

``` {#codeblock_d79_4yb_tbi}
message CreateIndexRequest {
    required string main_table_name = 1;
    required IndexMeta index_meta = 2;
    optional bool include_base_data = 3;
}
```

 **main\_table\_name:** 

-   类型：string
-   是否必要参数：是
-   索引表所在主表的名字

 **index\_meta:** 

-   类型：IndexMeta \(链接）
-   是否必要参数：是
-   索引表的schema

 **include\_base\_data：** 

-   类型：bool
-   是否必要参数：否
-   索引表中，是否包含在创建索引表前，主表的存量数据。

## 响应消息结构 {#section_wp4_ygt_4fb .section}

``` {#codeblock_quo_krt_zkh}


message CreateIndexResponse {
}
```

