# PrimaryKeySchema {#reference323 .reference}

主键的属性值。

## 数据结构 {#section_wny_wsq_dfb .section}

```language-protobuf
message PrimaryKeySchema {
    required string name = 1;
    required PrimaryKeyType type = 2;
    optional PrimaryKeyOption option = 3; 
}

```

name:

-   类型：string

-   描述：该列的列名。


type:

-   类型：[PrimaryKeyType](intl.zh-CN/API 参考/DataType/PrimaryKeyType.md#) 

-   描述：该列的类型。


option:

-   类型：[PrimaryKeyOption](intl.zh-CN/API 参考/DataType/PrimaryKeyOption.md#) 

-   描述：该列的附加属性值。


