# Query by wildcard character {#concept_n4x_zbk_pgb .concept}

You can call the WildcardQuery operation to match data based on wildcard characters.

You can specify a value to be matched as a string that contains one or more wildcard characters. An asterisk \(\*\) represents any number of characters or an empty string. A question mark \(?\) represents any single character. For example, if you search for string "table\*e", you can retrieve query results such as "tablestore". The string specified as a filtering condition cannot start with an asterisk \(\*\).

```
query = WildcardQuery('k', 'key00*')
rows, next_token, total_count, is_all_succeed = client.search(
    table_name, index_name, 
    SearchQuery(query, limit=100, get_total_count=True), 
    ColumnsToGet(return_type=ColumnReturnType.ALL)
)
```

