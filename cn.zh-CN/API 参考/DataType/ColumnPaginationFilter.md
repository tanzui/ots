# ColumnPaginationFilter {#reference282 .reference}

宽行读取过滤条件，适用于filter。

## 数据结构 {#section_zrt_h4q_dfb .section}

```language-protobuf
message ColumnPaginationFilter {
    required int32 offset = 1;
    required int32 limit = 2;
}

```

offset:

-   类型：int32

-   描述：起始列的位置，表示从第几列开始读。


limit:

-   类型：int32

-   描述：读取的列的个数。


## 相关操作 { .section}

Filter

[GetRow](cn.zh-CN/API 参考/API 概览/GetRow.md#)

[GetRange](cn.zh-CN/API 参考/API 概览/GetRange.md#)

[BatchGetRow](cn.zh-CN/API 参考/API 概览/BatchGetRow.md#)

