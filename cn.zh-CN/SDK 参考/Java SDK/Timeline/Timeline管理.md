# Timeline管理 {#concept_263746 .concept}

Timeline管理提供了消息模糊查询、多条件组合查询接口。

Timeline管理的查询功能基于多元索引，只有设置了IndexSchema的TimelineStore才支持。索引类型支持LONG、DOUBLE、BOOLEAN、KEYWORD、GEO\_POINT、TEXT等类型，属性包含Index、Store、Array以及分词器，其含义与多元索引相同，具体请参考[概述](../../../../cn.zh-CN/产品功能/多元索引/使用多元索引/概述.md#)。

## Search {#section_72k_ksv_htb .section}

多维度组合查询，需要模糊查询的字段，按照需求创建TEXT类型，并设置需要的分词器。

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

批量写基于表格存储SDK中DefaultTableStoreWriter实现，可以主动调用flush接口，将Buffer中尚未发出的请求主动触发发送，同步等待至所有消息写入成功。

``` {#codeblock_8rz_331_ehn}
/**
 * Flush messages in buffer, and wait until all messages are stored.
 * */
timelineStore.flush();
```

