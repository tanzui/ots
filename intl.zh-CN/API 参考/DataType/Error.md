# Error {#reference309 .reference}

用于在操作失败时的响应消息中表示错误信息，以及在 BatchGetRow 和 BatchWriteRow 操作的响应消息中表示单行请求的错误。

## 数据结构 {#section_j5n_mqq_dfb .section}

``` {#codeblock_vp6_czr_0jt .language-xml}
Error {
    required string code = 1;
    optional string message = 2;
}
				
```

code：

-   类型：string

-   描述：当前单行操作的错误码。


message：

-   类型：string

-   描述：当前单行操作的错误信息，具体含义可参考错误码。


## 相关操作 {#section_vd6_9uu_ebz .section}

[BatchGetRow](intl.zh-CN/API 参考/API 概览/BatchGetRow.md#)

[BatchWriteRow](intl.zh-CN/API 参考/API 概览/BatchWriteRow.md#)

