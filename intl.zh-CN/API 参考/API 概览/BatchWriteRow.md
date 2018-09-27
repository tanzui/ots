# BatchWriteRow {#reference1996 .reference}

批量插入、修改或删除一个或多个表中的若干行数据。

BatchWriteRow 操作可视为多个 PutRow、UpdateRow、DeleteRow 操作的集合。各个操作独立执行，独立返回结果，独立计算服务能力单元。

与执行大量的单行写操作相比，使用 BatchWriteRow 操作可以有效减少请求的响应时间，提高数据的写入速率。

## 请求结构： { .section}

```language-pb
message BatchWriteRowRequest {
    repeated TableInBatchWriteRowRequest tables = 1;
}

```

tables:

-   类型：repeated [TableInBatchWriteRowRequest](intl.zh-CN/API 参考/DataType/TableInBatchWriteRowRequest.md#) 

-   是否必要参数：是

-   指定了需要要执行写操作的行信息。

-   以下情况都会返回整体错误：

    -   tables 中任一表不存在。

    -   tables 中包含同名的表。

    -   tables 中任一表名表名不符合[表名命名规范](../../../../intl.zh-CN/产品功能/表格存储的表.md#section_wml_skg_cfb)。

    -   tables 中任一行操作未指定主键、主键列名称不符合规范或者主键列类型不正确。

    -   tables 中任一属性列名称不符合[列名命名规范](../../../../intl.zh-CN/数据模型/Wide Column/基本概念/主键和属性.md#)。

    -   tables 中任一行操作存在与主键列同名的属性列。

    -   tables 中任一主键列或者属性列的值大小超过[上限]()。

    -   tables 中任一表中存在主键完全相同的请求。

    -   tables 中所有表总的行操作个数超过 200 个，或者其含有的总数据大小超过 4 M。

    -   tables 中任一表内没有包含行操作,则返回 OTSParameterInvalidException 的错误。

    -   tables 中任一 PutRowInBatchWriteRowRequest 包含的 Column 个数超过 1024 个。

    -   tables 中任一 UpdateRowInBatchWriteRowRequest 包含的 ColumnUpdate 个数超过 1024 个。


## 响应消息结构： { .section}

```language-pb
message BatchWriteRowResponse {
    repeated TableInBatchWriteRowResponse tables = 1;
}

```

tables:

-   类型：[TableInBatchWriteRowResponse](intl.zh-CN/API 参考/DataType/TableInBatchWriteRowResponse.md#) 

-   对应了每个 table 下各操作的响应信息，包括是否成功执行，错误码和消耗的服务能力单元。

-   响应消息中 TableInBatchWriteRowResponse 对象的顺序与 BatchWriteRowRequest 中的 TableInBatchWriteRowRequest 对象的顺序相同；每个 TableInBatchWriteRowRequest 中 put\_rows、update\_rows、delete\_rows 包含的 RowInBatchWriteRowResponse 对象的顺序分别与 TableInBatchWriteRowRequest 中 put\_rows、update\_rows、delete\_rows 包含的 PutRowInBatchWriteRowRequest、UpdateRowInBatchWriteRowRequest 和 DeleteRowInBatchWriteRowRequest 对象的顺序相同。

-   若某行读取失败，该行所对应的 RowInBatchWriteRowResponse 中 is\_ok 将为 false。


**说明：** BatchWriteRow 操作可能会在行级别部分失败，此时返回的 HTTP 状态码仍为 200。应用程序必须对 RowInBatchWriteRowResponse 中的 error 进行检查，确认每一行的执行结果并进行相应的处理。

服务能力单元消耗：

-   如果本次操作整体失败，不消耗任何服务能力单元。

-   如果请求超时，结果未定义，服务能力单元有可能被消耗，也可能未被消耗。

-   其他情况将每个 PutRowInBatchWriteRowRequest、UpdateRowInBatchWriteRowRequestDelete、RowInBatchWriteRowRequest 依次视作相对应的写操作独立计算读服务能力单元。请参考[PutRow 服务能力单元消耗](intl.zh-CN/API 参考/API 概览/PutRow.md#)，[UpdateRow 服务能力单元消耗](intl.zh-CN/API 参考/API 概览/UpdateRow.md#)和[DeleteRow 服务能力单元消耗](intl.zh-CN/API 参考/API 概览/DeleteRow.md#)。


