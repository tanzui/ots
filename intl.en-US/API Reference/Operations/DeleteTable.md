# DeleteTable {#reference242 .reference}

DeleteTable deletes the specified table under this instance.

## Request structure: { .section}

```language-pb
message DeleteTableRequest {
    required string table_name = 1;
}

```

table\_name:

-   Type: string

-   Required Parameter: Yes

-   The name of the table to be deleted.


## Response message structure: { .section}

```language-pb
message DeleteTableResponse {
}

```

If the DeleteTable response does not contain an error, then the table has been deleted.

