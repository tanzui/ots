# ListTable {#reference266 .reference}

ListTable obtains the names of all tables under the current instance.

## Request structure: { .section}

```language-pb
message ListTableRequest {
}

```

## Response message structure: { .section}

```language-pb
message ListTableResponse {
    repeated string table_names = 1;
}

```

table\_names:

-   Type: repeated string

-   The names of all tables under the current instance.


**Note:** If a table has been newly created, its table name appears in ListTableResponse. However, no read/write operation can be performed on a table immediately after it is created. Generally, a read/write operation can be performed on a new table one minute after it is created.

