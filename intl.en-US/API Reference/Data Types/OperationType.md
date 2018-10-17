# OperationType {#reference196 .reference}

In UpdateRow, OperationType indicates the modification method for one column. The types of operations include:

-   PUT, which indicates that a column is inserted or this column’s data is overwritten.

-   UPDATE, which indicates that a column is updated.

-   DELETE, which indicates that a column is deleted.


## Enumeration value list {#section_hbg_bsq_dfb .section}

```language-xml
enum OperationType {
    PUT = 1;
    UPDATE = 2;
    DELETE = 3;
}

```

