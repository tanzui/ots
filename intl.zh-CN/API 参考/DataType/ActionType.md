# ActionType {#reference260 .reference}

在 GetStreamRecord 的响应消息中，表示操作类型。

-   PUT\_ROW 表示此次操作类型是PutRow

-   UPDATE\_ROW 表示此次操作类型是UpdateRow

-   DELETE\_ROW 表示此次操作类型是DeleteRow


## 枚举取值列表 {#section_z4g_knq_dfb .section}

```language-xml
enum ActionType {
    PUT_ROW = 1;
    UPDATE_ROW = 2;
    DELETE_ROW = 3;
}

```

## 相关操作 { .section}

[GetStreamRecord](intl.zh-CN/API 参考/API 概览/GetStreamRecord.md#)

