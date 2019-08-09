# Query by prefix {#concept_s4j_ybk_pgb .concept}

You can call the PrefixQuery operation to query data that matches a specified prefix.

When a table contains a text string, Table Store tokenizes the string and matches any of the tokens with the specified prefix.

```
query = PrefixQuery('k', 'key00')
rows, next_token, total_count, is_all_succeed = client.search(
    table_name, index_name, 
    SearchQuery(query, limit=100, get_total_count=True), 
    ColumnsToGet(return_type=ColumnReturnType.ALL)
)
```

