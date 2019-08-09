# Query by match {#concept_uzp_xbk_pgb .concept}

This topic describes matching operations in search index-based queries.

## MatchAllQuery {#section_hwy_jgk_pgb .section}

You can call the MatchAllQuery operation to query the total number of rows or any number of rows in a table.

``` {#codeblock_bj8_hih_uwh}
query = MatchAllQuery()
all_rows = []
next_token = None

while not all_rows or next_token:
    rows, next_token, total_count, is_all_succeed = client.search(table_name, index_name,
        SearchQuery(query, next_token=next_token, limit=100, get_total_count=True),
        columns_to_get=ColumnsToGet(['k', 't', 'g', 'ka', 'la'], ColumnReturnType.SPECIFIED))
    all_rows.extend(rows)

for row in all_rows:
    print row

print 'Total rows:', len(all_rows)
```

## MatchQuery {#section_iwy_jgk_pgb .section}

You can call the MatchQuery operation to query a table based on approximate matches.

For example, if you search for the phrase "this is", you can obtain query results such as "..., this is tablestore", "is this tablestore", "tablestore is cool", "this", and "is".

``` {#codeblock_tjr_nzg_578}
query = MatchQuery('t', 'this is 0')
rows, next_token, total_count, is_all_succeed = client.search(
    table_name, index_name, 
    SearchQuery(query, limit=100, get_total_count=True), 
    ColumnsToGet(return_type=ColumnReturnType.ALL)
)
```

## MatchPhraseQuery {#section_jwy_jgk_pgb .section}

You can call the MatchPhraseQuery operation to specify a phrase as a filtering condition. MatchPhraseQuery is similar to MatchQuery. The difference is that MatchPhraseQuery matches a phrase as a whole. For example, if you search for the phrase "this is", you can obtain query results such as "..., this is tablestore" and "this is a table". You cannot obtain query results such as "this table is ..." and "is this a table".

``` {#codeblock_j0r_rhs_ve3}
query = MatchPhraseQuery('t', 'this is')
rows, next_token, total_count, is_all_succeed = client.search(
    table_name, index_name, 
    SearchQuery(query, limit=100, get_total_count=True), 
    ColumnsToGet(return_type=ColumnReturnType.ALL)
)
```

