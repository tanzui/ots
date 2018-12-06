# BatchGetRow {#reference1686 .reference}

批量读取一个或多个表中的若干行数据。

BatchGetRow 操作可视为多个 GetRow 操作的集合，各个操作独立执行，独立返回结果，独立计算服务能力单元。

与执行大量的 GetRow 操作相比，使用 BatchGetRow 操作可以有效减少请求的响应时间，提高数据的读取速率。

## 请求结构： { .section}

```language-pb
message BatchGetRowRequest {
    repeated TableInBatchGetRowRequest tables = 1;
}

```

tables:

-   类型：repeated [TableInBatchGetRowRequest](intl.zh-CN/API 参考/DataType/TableInBatchGetRowRequest.md#) 

-   是否必要参数：是

-   指定了需要要读取的行信息。

-   若 tables 中出现了下述情况，则操作整体失败，返回错误。

    -   tables 中任一表不存在。

    -   tables 中任一表名不符合[表名命名规范](../../../../intl.zh-CN/产品功能/表格存储的表.md#section_wml_skg_cfb)。

    -   tables 中任一行未指定主键、主键名称不符合规范或者主键类型不正确。

    -   tables 中任一表的 columns\_to\_get 内的列名不符合[列名命名规范](../../../../intl.zh-CN/数据模型/Wide Column/基本概念/主键和属性.md#)。

    -   tables 中包含同名的表。

    -   tables 中任一表内包含主键完全相同的行。

    -   所有 tables 中 RowInBatchGetRowRequest 的总个数超过 100 个。

    -   tables 中任一表内不包含任何 RowInBatchGetRowRequest。

    -   tables 中任一表的 columns\_to\_get 超过 128 列。


## 响应消息结构： { .section}

```language-pb
message BatchGetRowResponse {
    repeated TableInBatchGetRowResponse tables = 1;
}

```

tables:

-   类型：repeated [TableInBatchGetRowResponse](intl.zh-CN/API 参考/DataType/TableInBatchGetRowResponse.md#) 

-   对应了每个 table 下读取到的数据。

-   响应消息中 TableInBatchGetRowResponse 对象的顺序与 BatchGetRowRequest 中的 TableInBatchGetRowRequest 对象的顺序相同；每个 TableInBatchGetRowResponse 下面的 RowInBatchGetRowResponse 对象的顺序与 TableInBatchGetRowRequest 下面的 RowInBatchGetRowRequest 相同。

-   如果某行不存在或者某行在指定的 columns\_to\_get 下没有数据，仍然会在 TableInBatchGetRowResponse 中有一条对应的 RowInBatchGetRowResponse，但其 row 下面的 primary\_key\_columns 和 attribute\_columns 将为空。

-   若某行读取失败，该行所对应的 RowInBatchGetRowResponse 中 is\_ok 将为 false，此时 row 将为空。


**说明：** BatchGetRow 操作可能会在行级别部分失败，此时返回的 HTTP 状态码仍为 200。应用程序必须对 RowInBatchGetRowResponse 中的 error 进行检查确认每一行的执行结果，并进行相应的处理。

服务能力单元消耗：

-   如果本次操作整体失败，不消耗任何服务能力单元。

-   如果请求超时，结果未定义，服务能力单元有可能被消耗，也可能未被消耗。

-   其他情况将每个 RowInBatchGetRowRequest 视为一个 GetRow 操作独立计算写服务能力单元，具体请参考 [GetRow 服务能力单元消耗](intl.zh-CN/API 参考/API 概览/GetRow.md#section_gxh_25t_1z)。


