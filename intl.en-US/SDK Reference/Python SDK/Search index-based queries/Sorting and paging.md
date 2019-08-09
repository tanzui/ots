# Sorting and paging {#concept_yw3_cck_pgb .concept}

This topic describes the sorting and paging methods in search index-based queries.

## IndexSort {#section_edt_q3k_pgb .section}

The matched data is sorted based on the value of the IndexSort field value when search index-based queries are used. Indexes of the nested type do not support IndexSort. The default value of the IndexSort field is the value of primary key columns. You can specify the IndexSort field when creating an index.

IndexSort determines the default order of the data that Table Store returns in search index-based queries. If you do not specify the IndexSort field, Table Store returns the query result based on the order of primary key columns.

## Specify a sorting method {#section_fdt_q3k_pgb .section}

You can specify a sorting method for each query. Search index-based queries support the following sorting methods. You can also specify multiple sorting methods with different priorities as needed.

 **ScoreSort** 

You can use ScoreSort to sort the query result by keyword relevance score. ScoreSort is applicable to scenarios such as full-text indexing. Note that you must set ScoreSort to sort the query result by keyword relevance score. Otherwise, the query result is sorted based on the value of the IndexSort field.

Set sorting fields as follows:

``` {#codeblock_dfx_86v_dzt}
score_sort = ScoreSort(sort_order=SortOrder.DESC)
```

 **PrimaryKeySort** 

You can use PrimaryKeySort to sort the query result based on the order of primary key columns.

Set sorting fields as follows:

``` {#codeblock_ph9_ok9_fvd}
primary_key_sort = PrimaryKeySort(sort_order=SortOrder.DESC)
```

 **FieldSort** 

You can use FieldSort to sort the query result based on the order of a specified column.

Set sorting fields as follows:

``` {#codeblock_zom_f0r_070}
field_sort = FieldSort('l', SortOrder.ASC)

nested_field_sort = FieldSort(
    'n.nl', 
    sort_order=SortOrder.ASC, 
    nested_filter=NestedFilter('n', RangeQuery('n.nl', range_from=150, range_to=200))
)
```

You can sort the query result based on the order of multiple columns.

Set sorting fields as follows:

``` {#codeblock_06p_7c9_6zk}
sort = Sort(
    sorters=[
        FieldSort('a', SortOrder.ASC),
        FieldSort('b', SortOrder.ASC)
    ]
)
```

 **GeoDistanceSort** 

You can use GeoDistanceSort to sort the query result based on geographical locations.

Set sorting fields as follows:

``` {#codeblock_swd_c4f_k7c}
geo_distance_sort = GeoDistanceSort('g', ['32.5,116.5', '32.0,116.0'], sort_order=SortOrder.DESC, sort_mode=SortMode.MAX) 

				
```

## Specify a paging method {#section_kdt_q3k_pgb .section}

Only fields with the EnableSortAndAgg attribute set to true can be sorted. You can set multiple filtering conditions for index sorting.

 **Use the limit and offset parameters** 

When the total number of rows to be obtained is less than 2,000, you can specify the limit and offset parameters for paging. For example, you can set limit to 100 and offset to 100 to obtain 100 rows of data starting from Row 100. The sum of the limit and offset parameter values cannot exceed 2,000.

``` {#codeblock_7re_wke_dvb}
query = RangeQuery('k', 'key100', 'key500', include_lower=False, include_upper=False)
rows, next_token, total_count, is_all_succeed = client.search(
    table_name, index_name, 
    SearchQuery(query, offset=100, limit=100, get_total_count=True), 
    ColumnsToGet(return_type=ColumnReturnType.ALL)
)
			
```

 **Use a token** 

If Table Store does not complete reading the required data, Table Store returns next\_token, which can be used to continue reading the subsequent data. When you use a token for paging, the sorting method is the same as that used in the previous request. If a token is used, the offset parameter cannot be used.

``` {#codeblock_qj4_rs3_h18}
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

