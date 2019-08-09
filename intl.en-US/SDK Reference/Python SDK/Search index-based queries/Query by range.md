# Query by range {#concept_xhb_zbk_pgb .concept}

You can call the RangeQuery operation to query data that falls within a specified range.

For a table that contains a text string, Table Store tokenizes the string and matches any of the tokens that falls within the specified range.

```
query = RangeQuery('k', 'key100', 'key200', include_lower=False, include_upper=False)
rows, next_token, total_count, is_all_succeed = client.search(
    table_name, index_name, 
    SearchQuery(query, limit=100, get_total_count=True), 
    ColumnsToGet(return_type=ColumnReturnType.ALL)
)
```

