# RowInBatchGetRowResponse {#reference649 .reference}

在 BatchGetRow 操作的返回消息中，表示一行数据。

## 数据结构 {#section_jb1_tvq_dfb .section}

```language-xml
message RowInBatchGetRowResponse {
    required bool is_ok = 1 [default = true];
    optional Error error = 2;
    optional ConsumedCapacity consumed = 3;
    optional bytes row = 4; 
	optional bytes next_token = 5;
}

```

is\_ok:

-   类型：bool

-   描述：该行操作是否成功。若为 true，则该行读取成功，error 无效；若为 false，则该行读取失败，row 无效。


error:

-   类型：[Error](intl.zh-CN/API 参考/DataType/Error.md#)

-   描述：该行操作的错误信息。


consumed:

-   类型：[ConsumedCapacity](intl.zh-CN/API 参考/DataType/ConsumedCapacity.md#) 

-   描述：该行操作消耗的服务能力单元。


row:

-   类型：bytes

-   读取到的数据，由Plainbuffer编码，详见[Plainbuffer](intl.zh-CN/API 参考/DataType/PlainBuffer.md#)编码。

-   如果该行不存在，则数据为空。


next\_token:

-   类型：bytes

-   宽行读取时，下一次读取的起始位置，暂不可用。


## 相关操作 { .section}

 [BatchGetRow](intl.zh-CN/API 参考/API 概览/BatchGetRow.md#) 

