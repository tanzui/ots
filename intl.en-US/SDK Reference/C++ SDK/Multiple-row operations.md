# Multiple-row operations {#reference5294 .reference}

Table Store offers interfaces for multi-row operations such as BatchGetRow, BatchWriteRow, and GetRange.

**Note:** The following operation examples are based on synchronous interfaces. See [Asynchronous interfaces](reseller.en-US/SDK Reference/C++ SDK/Asynchronous interface.md#) for more information on how to operate asynchronous interfaces.

## BatchGetRow {#section_hvk_knf_2fb .section}

Batch reads several rows of data from one or more tables.

BatchGetRow can be seen as a set of multiple GetRow operations. Performing each operation, returning the results, and calculating the service capacity unit are all done independently. In comparison with performing a large number of GetRow operations, using BatchGetRow can effectively shorten the request response time and increase the data read rate.

Two possible errors may occur when using BatchGetRow:

-   Overall request error, such as a network error. These errors are stored in the return values of batchGetRow\(\).
-   No overall request error, but some individual rows contain errors. These errors are stored in the results of the corresponding rows.

**Example**

```language-cpp
BatchGetRowRequest req;
{
    MultiPointQueryCriterion& criterion = req.mutableCriteria().append();
    IVector<MultiPointQueryCriterion::RowKey>& rowkeys = criterion.mutableRowKeys();
    {
        MultiPointQueryCriterion::RowKey& exist = rowkeys.append();
        exist.mutableGet().append() = PrimaryKeyColumn(
            "pkey",
            PrimaryKeyValue::toStr("some key"));
        exist.mutableUserData() = &userDataForSomeKey;
    }
    {
        MultiPointQueryCriterion::RowKey& exist = rowkeys.append();
        exist.mutableGet().append() = PrimaryKeyColumn(
            "pkey",
            PrimaryKeyValue::toStr("another key"));
        exist.mutableUserData() = &userDataForAnotherKey;
    }
    criterion.mutableTable() = "YourTable";
    criterion.mutableMaxVersions().reset(1);
}
BatchGetRowResponse resp;
Optional<OTSError> res = client.batchGetRow(resp, req);

```

**Note:** Detailed code is available at [batchGetRow@GitHub](https://github.com/aliyun/aliyun-tablestore-cpp-sdk/tree/master/examples).

## BatchWriteRow { .section}

Batch insert, modify, or delete several rows of data from one or more tables.

BatchWriteRow can be seen as a set of multiple PutRow, UpdateRow, and DeleteRow operations. Performing each operation, returning the results, and calculating the service capacity unit are all done independently.

Two possible errors may occur when using BatchWriteRow:

-   Overall request error, such as a network timeout. These errors are stored in the return values of batchWriteRow\(\).
-   Errors in individual rows, such as an invalid primary key value. These errors are stored in every row of BatchWriteRowResponse.

**Example**

```language-cpp
static const char kPutRow[] = "PutRow";
static const char kUpdateRow[] = "UpdateRow";
static const char kDeleteRow[] = "DeleteRow";

BatchWriteRowRequest req;
{
    // put row
    BatchWriteRowRequest::Put& put = req.mutablePuts().append();
    put.mutableUserData() = kPutRow;
    put.mutableGet().mutableTable() = kTableName;
    PrimaryKey& pkey = put.mutableGet().mutablePrimaryKey();
    pkey.append() = PrimaryKeyColumn(
        "pkey",
        PrimaryKeyValue::toStr("row to put"));
}
{
    // update row
    BatchWriteRowRequest::Update& update = req.mutableUpdates().append();
    update.mutableUserData() = kUpdateRow;
    update.mutableGet().mutableTable() = kTableName;
    PrimaryKey& pkey = update.mutableGet().mutablePrimaryKey();
    pkey.append() = PrimaryKeyColumn(
        "pkey",
        PrimaryKeyValue::toStr("row to update"));
    RowUpdateChange::Update& attr = update.mutableGet().mutableUpdates().append();
    attr.mutableType() = RowUpdateChange::Update::kPut;
    attr.mutableAttrName() = "attr0";
    attr.mutableAttrValue().reset(AttributeValue::toStr("some value"));
}
{
    // delete row
    BatchWriteRowRequest::Delete& del = req.mutableDeletes().append();
    del.mutableUserData() = kDeleteRow;
    del.mutableGet().mutableTable() = kTableName;
    PrimaryKey& pkey = del.mutableGet().mutablePrimaryKey();
    pkey.append() = PrimaryKeyColumn(
        "pkey",
        PrimaryKeyValue::toStr("row to delete"));
}
BatchWriteRowResponse resp;
Optional<OTSError> res = client.batchWriteRow(resp, req);

```

**Note:** Detailed code is available at [batchWriteRow@GitHub](https://github.com/aliyun/aliyun-tablestore-cpp-sdk/tree/master/examples).

## GetRange { .section}

Read data within the specified primary key range.

`RangeIterator` is recommended. To construct RangeIterator, you must provide:

-    [AsyncClient](reseller.en-US/SDK Reference/C++ SDK/Initialization.md#section_czv_vkf_2fb).

-   RangeQueryCriterion is similar to PointQueryCriterion, but RangeQueryCriterion also requires:

    -   Set the starting point \(inclusive\) and ending point \(exclusive\) of the range. In addition to the normal primary key value, you can also use two special values: “negative infinity” \(strictly less than all normal primary key column values\) and “positive infinity” \(strictly greater than all primary key column values\).

    -   Set the reading to positive sequence \(values going from small to large\) or reverse sequence \(values going from large to small\). The default setting is positive sequence. When reading in positive sequence, the starting point must be smaller than the ending point. In the reverse sequence, the starting point must be greater than the ending point.


The RangeIterator object is an iterator that provides three interfaces:

-    `moveNext()` moves the RangeIterator object to the next row. A newly constructed RangeIterator object must call `moveNext()` to get the value. If reading data fails, `moveNext()` displays the error in the return value.

-    `valid()` shows whether the RangeIterator object has reached the ending point of the range.

-   If `valid()` is true, row objects can be read using `get()`. You can avoid memory replication by removing the contents of the row object returned by `get()`. After removing the content, the directly following `get()` returns the removed content.


**Example**

```language-cpp
RangeQueryCriterion query;
query.mutableTable() = "YourTable";
query.mutableMaxVersions().reset(1);
{
    PrimaryKey& start = query.mutableInclusiveStart();
    start.append() = PrimaryKeyColumn(
        "pkey",
        PrimaryKeyValue::toInfMin());
}
{
    PrimaryKey& end = query.mutableExclusiveEnd();
    end.append() = PrimaryKeyColumn(
        "pkey",
        PrimaryKeyValue::toInfMax());
}
auto_ptr<AsyncClient> aclient(AsyncClient::create(client));
RangeIterator iter(*aclient, query);
for(;;) {
    Optional<OTSError> err = iter.moveNext();
    if (err.present()) {
        // do something with err
        abort();
    }
    if (! iter.valid()) {
        break;
    }
    Row& row = iter.get();
    // do something with row
}

```

**Note:** Detailed code is available at [GetRange@GitHub](https://github.com/aliyun/aliyun-tablestore-cpp-sdk/tree/master/examples).

## Read specified columns { .section}

Table Store supports infinite row width. Typically, reading the entire row is not required, as reading specified columns is enough. `QueryCriterion` \(PointQueryCriterion, MultiPointQueryCriterion, and RangeQueryCriterion\) provides `mutableColumnsToGet()` to specify columns that must be read. They can be attribute columns or primary key columns. If the value is empty, the entire row is read.

If a specified column does not exist on the read row, the column is missing from the returned result. Table Store does not provide placeholders.

In GetRange, if all specified columns are attribute columns, and a row in the range misses all specified columns, this row does not appear in the result. If this row must be noticed, add the primary key column to the specified columns.

## Read specified versions { .section}

Each attribute column can contain multiple versions, with each version number \(timestamp\) corresponding to a column value. When reading, you can define the number of versions \(`mutableMaxVersions()`\) and the range of versions \(`mutableTimeRange()`\) to be read.

At least one of the maxversions and the range of versions must be defined.

-   If you only define the number of versions, the defined amount of data in all versions is returned, from the newest to the oldest.
-   If you only define the range of versions, all data within this range is returned.
-   If you define both the number of versions and the range of versions, the defined amount of data within the version range is returned, from the newest to the oldest.

## Conditional write { .section}

In conditional writing, certain conditions have to be checked and met before rows can be written. Table Store guarantees the atomicity of the condition checks and writing.

Table Store supports row existence conditions and column value conditions:

-   Row existence conditions can be divided into the following types:

    -   Ignore: This is the default setting whether or not a row exists.
    -   ExpectExist: expected to exist. Write if a row exists.
    -   ExpectNotExist: expected not to exist. Write if a row does not exist.
-   The column value condition is equivalent to the [Filter](reseller.en-US/SDK Reference/C++ SDK/Filter.md#).


