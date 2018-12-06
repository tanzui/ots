# StreamRecord {#reference303 .reference}

在 GetStreamRecord 的响应消息中，表示一行数据。

## 数据结构 {#section_ysh_jyq_dfb .section}

```language-xml
 message StreamRecord {
        required ActionType action_type = 1;
        required bytes record = 2;
}

```

action\_type:

-   类型：required [ActionType](intl.zh-CN/API 参考/DataType/ActionType.md#) 

-   描述：表示该行的操作类型。


record:

-   类型：required bytes

-   描述：表示该行的数据内容。


## 相关操作 { .section}

 [GetStreamRecord](intl.zh-CN/API 参考/API 概览/GetStreamRecord.md#) 

