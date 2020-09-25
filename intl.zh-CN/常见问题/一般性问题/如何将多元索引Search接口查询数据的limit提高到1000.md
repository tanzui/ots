# 如何将多元索引Search接口查询数据的limit提高到1000

介绍将多元索引Search接口查询数据的limit提高到1000的方法。

为了提高使用多元索引Search接口单次查询的返回结果数，当查询数据时只查询多元索引中的数据没有反查数据表时，则limit限制自适应提高到1000，如果查询数据时需要反查数据表，则limit限制为100。

## 操作步骤

通过如下操作可以将多元索引Search接口查询数据的limit提高到1000。

1.  创建多元索引时，将指定列的附加存储设置为true。
    -   当通过控制台创建多元索引时，附加存储默认为true，无需设置。
    -   当通过SDK创建多元索引时，将指定列的“FieldSchema”参数中的“store”参数设置为true。
2.  通过多元索引Search接口查询数据时，设置SearchRequest的ColumnsToGet参数。

    ColumnsToGet参数中仅返回已设置附加存储的列，且不能包含“数组类型”、“geo地理位置类型”和“nested嵌套类型”的列，此时limit限制自适应提高到1000。

    **说明：** 如果ColumnsToGet参数中包含“数组类型”、“geo地理位置类型”或“nested嵌套类型”的列，通过多元索引Search接口查询数据时仍会触发反查数据表，则limit限制为100。


## 示例

此处以Java SDK为例介绍如何设置ColumnsToGet参数，其他语言的SDK实现类似，只需修改SearchRequest中的ColumnsToGet参数即可。

```
SearchQuery searchQuery = new SearchQuery();
searchQuery.setQuery(new MatchQuery());
searchQuery.setLimit(1000);

SearchRequest searchRequest = new SearchRequest(tableName, indexName, searchQuery);
ColumnsToGet columnsToGet = new ColumnsToGet();
columnsToGet.setReturnAll(false);
columnsToGet.setColumns(Arrays.asList("field_1", "field_2", "field_3"));  //设置返回列的名称，列的数据类型不能为数组类型、geo地理位置类型和nested嵌套类型，否则会反查数据表。
searchRequest.setColumnsToGet(columnsToGet);
SearchResponse response = client.search(searchRequest);

//java-sdk-5.6.1及之后版本支持在ColumnsToGet中设置returnAllColumnsFromIndex参数，获取所有索引上的属性列。

ColumnsToGet columnsToGet = new ColumnsToGet();
columnsToGet.setReturnAllFromIndex(true);
searchRequest.setColumnsToGet(columnsToGet);
SearchResponse response = client.search(searchRequest);
```

