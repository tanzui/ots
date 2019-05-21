# DeleteRow {#reference1493 .reference}

您可以使用DeleteRow接口删除一行数据。

## 请求消息结构 { .section}

```language-pb
message DeleteRowRequest {
    required string table_name = 1;
    required bytes primary_key = 2; // Plainbuffer编码为二进制
    required Condition condition = 3;
    optional ReturnContent return_content = 4; 
}
```

|名称|类型|是否必选|描述|
|--|--|----|--|
|table\_name|string|是|请求更新数据的表名。|
|primary\_key|bytes|是|删除行的主键，Plainbuffer格式，编码详见[Plainbuffer](cn.zh-CN/API 参考/DataType/PlainBuffer.md#)编码。|
|condition|[Condition](cn.zh-CN/API 参考/DataType/Condition.md#)|是| -   在数据更新前是否进行存在性检查，可以取下面两个值：

    -   IGNORE：表示不做行存在性检查。

    -   EXPECT\_EXIST：表示期望行存在。

-   若期待该行存在，但实际该行不存在，则本次删除操作会失败, 返回错误；若忽视该行是否存在，则无论该行实际是否存在，都不会因此导致操作失败。


 |
|return\_content|[ReturnContent](cn.zh-CN/API 参考/DataType/ReturnContent.md#)|否|写入成功后返回的数据类型。目前仅支持返回主键，主要用于主键列自增功能中。|

## 响应消息结构 { .section}

```language-pb
message DeleteRowResponse {
    required ConsumedCapacity consumed = 1;
    optional bytes row = 2;
}
```

|名称|类型|描述|
|--|--|--|
|consumed|[ConsumedCapacity](cn.zh-CN/API 参考/DataType/ConsumedCapacity.md#)|本次操作[消耗的服务能力单元](#section_x9r_auo_rlz)。|
|row|bytes| -   当设置了return\_content后，返回的数据。
-   如果没有设置return\_content或者没有返回值，此处为NULL。
-   Plainbuffer格式，编码详见[Plainbuffer](cn.zh-CN/API 参考/DataType/PlainBuffer.md#)编码。

 |

## 服务能力单元消耗 {#section_x9r_auo_rlz .section}

-   如果该行不存在：

    -   若指定条件检查为 IGNORE，消耗写服务能力单元的数值为该行主键数据大小除以 4 KB 向上取整。

    -   若指定条件检查为 EXPECT\_EXIST，删除该行失败，消耗 1 单位的写 CU 和 1 单位的读 CU。

-   如果该行存在：

    -   若指定条件检查为 IGNORE，消耗写服务能力单元的数值为该行主键数据大小除以 4 KB 向上取整。

    -   若指定条件检查为 EXPECT\_EXIST，除了消耗该行主键数据大小除以 4 KB 向上取整的写 CU，还需消耗该行主键数据大小除以 4 KB 向上取整的读 CU。

    关于数据大小的计算请参见[产品定价](../../../../cn.zh-CN/产品定价/计量项和计费说明.md#)。

-   如果返回内部错误（HTTP 状态码：5XX），则此次操作不消耗服务能力单元；其他错误情况消耗 1 个写服务能力单元。

-   如果请求超时，结果未定义，服务能力单元有可能被消耗，也可能未被消耗。


