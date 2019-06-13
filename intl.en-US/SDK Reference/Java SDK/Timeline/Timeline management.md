# Timeline management {#concept_263746 .concept}

You can call the operations for the fuzzy query and bool query to manage Timeline data. The query operations work on the basis of the Search Index feature. Only the TimelineStore that has IndexSchema configured supports the query operations. An index can be LONG, DOUBLE, BOOLEAN, KEYWORD, GEO\_POINT, or TEXT type. The index attributes include Index, Store, Array, and Analyzer, and have the same descriptions as those of the Search Index feature. For more information, see [Overview](../../../../intl.en-US/Functions/SearchIndex/API operations/Overview.md#).

## Search {#section_72k_ksv_htb .section}

You can call this operation to use the bool query. This query requires the field for a fuzzy query. You need to set the index type of the field to TEXT, and specify the tokenizer.

``` {#codeblock_ik4_jdk_0pn}
/**
 * Search timeline by SearchParameter.
 * */
SearchParameter searchParameter = new SearchParameter(
        field("text").equals("fieldValue")
);
timelineStore.search(searchParameter);

/**
 * Search timeline by SearchQuery.
 * */
TermQuery query = new TermQuery();
query.setFieldName("text");
query.setTerm(ColumnValue.fromString("fieldValue"));
SearchQuery searchQuery = new SearchQuery().setQuery(query).setLimit(10);
timelineStore.search(searchQuery);
```

## Flush {#section_orw_zif_v25 .section}

The BatchStore operation works on the basis of the DefaultTableStoreWriter class in the SDK of Table Store. You can call the flush operation to trigger the process of sending the undelivered messages in the Buffer to Table Store and wait until Table Store stores all these messages.

``` {#codeblock_8rz_331_ehn}
/**
 * Flush messages in buffer, and wait until all messages are stored.
 * */
timelineStore.flush();
```

