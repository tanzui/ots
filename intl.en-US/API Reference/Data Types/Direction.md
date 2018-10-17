# Direction {#reference189 .reference}

Direction indicates the data query order in the GetRange operation. The two Direction operations are:

-   FORWARD, which indicates that the query proceeds from the smallest to the largest primary key.

-   BACKWARD, which indicates that the query proceeds from the largest to the smallest primary key.


## Enumeration value list {#section_bvh_3qq_dfb .section}

```language-xml
enum Direction {
    FORWARD = 0;
    BACKWARD = 1;
}

```

## Related operations { .section}

[GetRange](reseller.en-US/API Reference/Operations/GetRange.md#)

