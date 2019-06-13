# Meta management {#concept_263745 .concept}

You can call some operations, such as Insert, Delete, Update, Read, and Search, to manage Meta data. The Search operation works on the basis of the Search Index feature. Only the MetaStore that has IndexSchema configured supports the Search operation. An index can be LONG, DOUBLE, BOOLEAN, KEYWORD, or GEO\_POINT type. The index attributes include Index, Store, and Array, and have the same descriptions as those of the Search Index feature. For more information, see [Overview](../../../../intl.en-US/Functions/SearchIndex/API operations/Overview.md#).

## Insert {#section_y2j_db3_etm .section}

The TimelineIdentifer value is used to identify Timeline data. Table Store overwrites repeated Identifier values.

``` {#codeblock_2fa_jj7_cf1}
TimelineIdentifier identifier = new TimelineIdentifier.Builder()
        .addField("timeline_id", "group")
        .build();
TimelineMeta meta = new TimelineMeta(identifier)
        .setField("filedName", "fieldValue");

timelineMetaStore.insert(meta);
```

## Read {#section_jji_nl9_a8b .section}

You can cal this operation to read TimelineMeta data in one row based on the Identifier value.

``` {#codeblock_od9_y50_tkv}
TimelineIdentifier identifier = new TimelineIdentifier.Builder()
        .addField("timeline_id", "group")
        .build();

timelineMetaStore.read(identifier);
```

## Update {#section_5j3_iad_4g9 .section}

You can call this operation to update the Meta attribute that corresponds to the specified TimelineIdentifier value.

``` {#codeblock_b4b_i44_aw3}
TimelineIdentifier identifier = new TimelineIdentifier.Builder()
        .addField("timeline_id", "group")
        .build();
TimelineMeta meta = new TimelineMeta(identifier)
        .setField("filedName", "new value");

timelineMetaStore.update(meta);
```

## Delete {#section_hyi_lwc_tzq .section}

You can call this operation to delete the TimelineMeta data in one row based on the Identifier value.

``` {#codeblock_h62_mjp_5f6}
TimelineIdentifier identifier = new TimelineIdentifier.Builder()
        .addField("timeline_id", "group")
        .build();

timelineMetaStore.delete(identifier);
```

## Search {#section_v5m_kuc_4xm .section}

You can call this operation to specify two search parameters: SearchParameter and the native SDK class SearchQuery. This operation returns Iterator<TimelineMeta\>. You can iterate all result sets by using the iterator.

``` {#codeblock_8bm_qod_1wg}
/**
 * Search meta by SearchParameter.
 * */
SearchParameter parameter = new SearchParameter(
        field("fieldName").equals("fieldValue")
);
timelineMetaStore.search(parameter);

/**
 * Search meta by SearchQuery.
 * */
TermQuery query = new TermQuery();
query.setFieldName("fieldName");
query.setTerm(ColumnValue.fromString("fieldValue"));

SearchQuery searchQuery = new SearchQuery().setQuery(query);
timelineMetaStore.search(searchQuery);
```

