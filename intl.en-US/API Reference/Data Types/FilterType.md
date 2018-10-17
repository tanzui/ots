# FilterType {#reference268 .reference}

FilterType specifies the type for condition updating or filtering. The types include:

-   FT\_SINGLE\_COLUMN\_VALUE, which is a single-column condition.

-   FT\_COMPOSITE\_COLUMN\_VALUE, which is a multi-column composite condition.

-   FT\_COLUMN\_PAGINATION, which is a condition for wide-row read.


## Enumeration value list {#section_fpy_wrq_dfb .section}

```language-protobuf
enum FilterType {
    FT_SINGLE_COLUMN_VALUE = 1;
    FT_COMPOSITE_COLUMN_VALUE = 2;
    FT_COLUMN_PAGINATION = 3;
}

```

