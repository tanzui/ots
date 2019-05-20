# UpdateRow {#reference2046 .reference}

您可以使用UpdateRow接口更新指定行的数据。

**说明：** 如果指定行不存在，则新增一行；若指定行存在，则根据请求的内容在这一行中新增、修改或者删除指定列的值。

## 请求消息结构 { .section}

```language-protobuf
message UpdateRowRequest {
    required string table_name = 1;
    required bytes row_change = 2;
    required Condition condition = 3;
    optional ReturnContent return_content = 4; 
}
```

|名称|类型|是否必选|描述|
|--|--|----|--|
|table\_name|string|是| 请求更新数据的表名。

 |
|row\_change|bytes|是| -   更新的数据，包括主键和属性列，Plainbuffer格式，编码详见[Plainbuffer](cn.zh-CN/API 参考/DataType/PlainBuffer.md#)编码。

-   该行本次想要更新的全部属性列，表格存储会根据 row\_change中UpdateType的内容在这一行中新增、修改或者删除指定列的值。

-   该行已经存在的不在 row\_change 中的列将不受影响。

-   UpdateType 可以取下面的值：

    -   PUT：此时value 必须为有效的属性列值。语意为如果该列不存在，则新增一列；如果该列存在，则覆盖该列。

    -   DELETE：此时该 value 必须为空，需要指定timestamp。语意为删除该列特定版本的数据。

    -   DELETE\_ALL：此时该 value 和timestamp 都必须为空。语意为删除该列所有版本的数据。


 **说明：** 删除本行的全部属性列不等同于删除本行，若想删除本行，请使用 DeleteRow 操作。

 |
|condition|[Condition](cn.zh-CN/API 参考/DataType/Condition.md#)|是| -   在数据更新前是否进行存在性检查，可以取下面两个值：

    -   IGNORE 表示不做行存在性检查。

    -   EXPECT\_EXIST 表示期望行存在。

-   若期待该行存在但该行不存在，则本次更新操作会失败, 返回错误；若忽视该行是否存在，则无论该行是否存在，都不会因此导致本次操作失败。


 |
|return\_content|[ReturnContent](cn.zh-CN/API 参考/DataType/ReturnContent.md#)|否| 写入成功后返回的数据类型，目前仅支持返回主键，主要用于主键列自增功能中。

 |

## 响应消息结构 { .section}

```language-protobuf
message UpdateRowResponse {
    required ConsumedCapacity consumed = 1;
    optional bytes row = 2;
}
```

|名称|类型|描述|
|--|--|--|
|consumed|[ConsumedCapacity](cn.zh-CN/API 参考/DataType/ConsumedCapacity.md#)| 本次操作[消耗的服务能力单元](#title_444_bqk_2ce)。

 |
|row|bytes| -   当设置了return\_content后，返回的数据。

-   如果没设置return\_content或者没返回值，此处为NULL。

-   Plainbuffer格式，编码详见[Plainbuffer](cn.zh-CN/API 参考/DataType/PlainBuffer.md#)编码。


 |

## 服务能力单元消耗 {#section_w0n_3y0_tyk .section}

-   如果该行不存在：

    -   若指定条件检查为 IGNORE，消耗写服务能力单元的数值为本行的主键数据大小与要更新的属性列数据大小之和除以 4 KB 向上取整。如果 UpdateRow 中包含有需要删除的属性列，只有其列名长度计入该属性列数据大小。

    -   若指定条件检查为 EXPECT\_EXIST，本次插入失败并且消耗 1 单位写 CU 和 1 单位读 CU。

-   如果该行存在：

    -   若指定条件检查为 IGNORE，消耗写服务能力单元的数值为本行的主键数据大小与要更新的属性列数据大小之和除以 4 KB 向上取整。如果 UpdateRow 中包含有需要删除的属性列，只有其列名长度计入该属性列数据大小。

    -   若指定条件检查为 EXPECT\_EXIST，除了需要消耗在条件检查为 IGNORE 情况下的写 CU，还需消耗该行主键数据大小除以 4 KB 向上取整的读 CU。

    关于数据大小的计算请参见[产品定价](../../../../cn.zh-CN/产品定价/计量项和计费说明.md#)。

-   如果请求超时，结果未定义，服务能力单元有可能被消耗，也可能未被消耗。

-   如果返回内部错误（HTTP 状态码：5XX），则此次操作不消耗服务能力单元；其他错误情况消耗 1 个写服务能力单元和 1 个读服务能力单元。


