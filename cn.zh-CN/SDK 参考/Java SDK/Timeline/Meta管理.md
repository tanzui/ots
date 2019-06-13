# Meta管理 {#concept_263745 .concept}

Meta管理提供了增、删、改、单行读、多条件组合查询等接口。

Meta管理的多条件组合查询功能基于多元索引，只有设置了IndexSchema的MetaStore才支持。索引类型支持LONG、DOUBLE、BOOLEAN、KEYWORD、GEO\_POINT等类型，属性包含Index、Store和Array，其含义与多元索引相同，具体请参考[概述](../../../../cn.zh-CN/产品功能/多元索引/使用多元索引/概述.md#)。

## Insert {#section_y2j_db3_etm .section}

TimelineIdentifer是区分Timeline的唯一标识，重复的Identifier会被覆盖。

``` {#codeblock_2fa_jj7_cf1}
TimelineIdentifier identifier = new TimelineIdentifier.Builder()
        .addField("timeline_id", "group")
        .build();
TimelineMeta meta = new TimelineMeta(identifier)
        .setField("filedName", "fieldValue");

timelineMetaStore.insert(meta);
```

## Read {#section_jji_nl9_a8b .section}

根据Identifier读取单行TimelineMeta数据。

``` {#codeblock_od9_y50_tkv}
TimelineIdentifier identifier = new TimelineIdentifier.Builder()
        .addField("timeline_id", "group")
        .build();

timelineMetaStore.read(identifier);
```

## Update {#section_5j3_iad_4g9 .section}

更新TimelineIdentifier所对应的Meta属性。

``` {#codeblock_b4b_i44_aw3}
TimelineIdentifier identifier = new TimelineIdentifier.Builder()
        .addField("timeline_id", "group")
        .build();
TimelineMeta meta = new TimelineMeta(identifier)
        .setField("filedName", "new value");

timelineMetaStore.update(meta);
```

## Delete {#section_hyi_lwc_tzq .section}

根据Identifier删除单行TimelineMeta数据。

``` {#codeblock_h62_mjp_5f6}
TimelineIdentifier identifier = new TimelineIdentifier.Builder()
        .addField("timeline_id", "group")
        .build();

timelineMetaStore.delete(identifier);
```

## Search {#section_v5m_kuc_4xm .section}

提供两种查询参数，SearchParameter以及SDK原生类SearchQuery，返回Iterator<TimelineMeta\>，通过迭代器遍历。

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

