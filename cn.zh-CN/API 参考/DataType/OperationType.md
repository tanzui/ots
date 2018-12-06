# OperationType {#reference196 .reference}

在 UpdateRow 中，表示对一列的修改方式。

-   PUT 表示插入一列或覆盖该列的数据。

-   UPDATE 表示更新一列数据。

-   DELETE 表示删除该列。


## 枚举取值列表 {#section_hbg_bsq_dfb .section}

```language-xml
enum OperationType {
    PUT = 1;
    UPDATE = 2;
    DELETE = 3;
}

```

