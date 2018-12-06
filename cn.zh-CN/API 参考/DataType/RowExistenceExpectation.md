# RowExistenceExpectation {#reference283 .reference}

行存在性判断条件，枚举类型。

-   IGNORE 表示不做行存在性检查。

-   EXPECT\_EXIST 表示期待该行存在。

-   EXPECT\_NOT\_EXIST 表示期待该行不存在。


## 枚举取值列表 {#section_gr2_mvq_dfb .section}

```language-protobuf
enum RowExistenceExpectation {
    IGNORE = 0;
    EXPECT_EXIST = 1;
    EXPECT_NOT_EXIST = 2;
}

```

## 相关操作 { .section}

 [PutRow](intl.zh-CN/API 参考/API 概览/PutRow.md#) 

 [UpdateRow](intl.zh-CN/API 参考/API 概览/UpdateRow.md#) 

 [DeleteRow](intl.zh-CN/API 参考/API 概览/DeleteRow.md#) 

 [BatchWriteRow](intl.zh-CN/API 参考/API 概览/BatchWriteRow.md#) 

