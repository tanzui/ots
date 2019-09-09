# Error {#reference309 .reference}

用于在操作失败时的响应消息中表示错误信息，以及在 BatchGetRow 和 BatchWriteRow 操作的响应消息中表示单行请求的错误。

## 数据结构 {#section_j5n_mqq_dfb .section}

```language-xml
Error {
    required string code = 1;
    optional string message = 2;
}

```

code:

-   类型：string

-   描述：当前单行操作的错误码，具体含义可参考[错误码]()。


message:

-   类型：string

-   描述：当前单行操作的错误信息，具体含义可参考[错误码]()。


## 相关操作 { .section}

[BatchGetRow](cn.zh-CN/API 参考/API 概览/BatchGetRow.md#)

[BatchWriteRow](cn.zh-CN/API 参考/API 概览/BatchWriteRow.md#)

