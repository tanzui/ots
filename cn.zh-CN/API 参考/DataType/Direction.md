# Direction {#reference189 .reference}

在 GetRange 操作中，查询数据的顺序。

-   FORWARD 表示此次查询按照主键由小到大的顺序进行。

-   BACKWARD 表示此次查询按照主键由大到小的顺序进行。


## 枚举取值列表 {#section_bvh_3qq_dfb .section}

```language-xml
enum Direction {
    FORWARD = 0;
    BACKWARD = 1;
}

```

## 相关操作 { .section}

[GetRange](intl.zh-CN/API 参考/API 概览/GetRange.md#)

