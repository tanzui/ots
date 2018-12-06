# StreamSpecification {#reference310 .reference}

表示一个表的stream信息。

## 数据结构 {#section_d4f_syq_dfb .section}

```language-xml
message StreamSpecification {
    required bool enable_stream = 1;
    optional int32 expiration_time = 2;
}

```

enable\_stream:

-   类型：bool

-   描述：该表是否打开stream。


expiration\_time:

-   类型：int32

-   描述：该表的stream过期时间。


## 相关操作 { .section}

 [CreateTable](intl.zh-CN/API 参考/API 概览/CreateTable.md#) 

 [DescribeTable](intl.zh-CN/API 参考/API 概览/DescribeTable.md#) 

 [UpdateTable](intl.zh-CN/API 参考/API 概览/UpdateTable.md#) 

