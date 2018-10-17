# Single-row operations {#reference3686 .reference}

Table Store offers interfaces for single-row operations, such as PutRow, GetRow, UpdateRow, and DeleteRow.

**Note:** The following operation examples are based on synchronous interfaces. See [Asynchronous interfaces](reseller.en-US/SDK Reference/C++ SDK/Asynchronous interface.md#) for more information on how to operate asynchronous interfaces.

## Put in a row of data \(PutRow\) { .section}

The PutRow interface is used to insert a row of data. The PutRow interface is used to insert a row of data. If the row does not exist, this operation inserts a row. If the row exists, this operation overwrites it. \(This means that all columns and column values of all versions of the original row are deleted\).

**Example**

```language-cpp
PutRowRequest req;
{
    RowPutChange& chg = req.mutableRowChange();
    chg.mutableTable() = "YourTable";
    {
        // set primary key of the row to put
        PrimaryKey& pkey = chg.mutablePrimaryKey();
        pkey.append() = PrimaryKeyColumn(
            "pkey",
            PrimaryKeyValue::toStr("pkey-value"));
    }
    {
        // set attributes of the row to put
        IVector<Attribute>& attrs = chg.mutableAttributes();
        attrs.append() = Attribute(
            "attr",
            AttributeValue::toInteger(123));
    }
}
PutRowResponse resp;
Optional<OTSError> res = client.putRow(resp, req);

```

## Get the data of a row \(GetRow\) { .section}

The GetRow interface is used to read the data of a single row.

Specify the table name and the primary key of a row. The two possible reading results are as follows:

-   If this row exists, the GetRowResponse object returns each primary key column and attribute column for the row.

-   If this row does not exist, the GetRowResponse object does not include rows and it does not report errors.


**Example**

```language-cpp
GetRowRequest req;
{
    PointQueryCriterion& query = req.mutableQueryCriterion();
    query.mutableTable() = “YourTable”;
    {
        PrimaryKey& pkey = query.mutablePrimaryKey();
        pkey.append() = PrimaryKeyColumn(
            "pkey",
            PrimaryKeyValue::toStr("some_key")); // Assume the table has only one primary key column that is of the string type.
    }
    query.mutableMaxVersions().reset(1);
}
GetRowResponse resp;
Optional<OTSError> res = client.getRow(resp, req);

```

## Update the data of a row \(UpdateRow\) { .section}

The UpdateRow interface is used to update a row of data. If the row does not exist, a new row is written.

The update operation has the following four possible scenarios:

-   Writing a column value without specifying the version number. The Table Store service automatically offers a version number to make sure that the version number is incremented.

-   Writing a column value with a specified version number. If the column does not have the column value of this version, the data is inserted. If the column does have it, the original value is overwritten.

-   Delete the column values of the specified version.

-   Delete the column values of all versions for the entire column.


**Example**

```language-cpp
UpdateRowRequest req;
{
    RowUpdateChange& chg = req.mutableRowChange();
    chg.mutableTable() = "YourTable";
    {
        // set primary key of the row to put
        PrimaryKey& pkey = chg.mutablePrimaryKey();
        pkey.append() = PrimaryKeyColumn(
            "pkey",
            PrimaryKeyValue::toStr("pkey"));
    }
    {
        // insert a value without specifying version
        RowUpdateChange::Update& up = chg.mutableUpdates().append();
        up.mutableType() = RowUpdateChange::Update::kPut;
        up.mutableAttrName() = "attr0";
        up.mutableAttrValue().reset(AttributeValue::toStr("new value without specifying version"));
    }
    {
        // insert a value with version
        RowUpdateChange::Update& up = chg.mutableUpdates().append();
        up.mutableType() = RowUpdateChange::Update::kPut;
        up.mutableAttrName() = "attr1";
        up.mutableAttrValue().reset(AttributeValue::toStr("new value with version"));
        up.mutableTimestamp().reset(UtcTime::now());
    }
    {
        // delete a value with specific version
        RowUpdateChange::Update& up = chg.mutableUpdates().append();
        up.mutableType() = RowUpdateChange::Update::kDelete;
        up.mutableAttrName() = "attr2";
        up.mutableTimestamp().reset(UtcTime::now());
    }
    {
        // delete all values of a attribute column
        RowUpdateChange::Update& up = chg.mutableUpdates().append();
        up.mutableType() = RowUpdateChange::Update::kDeleteAll;
        up.mutableAttrName() = "attr3";
    }
}
UpdateRowResponse resp;
Optional<OTSError> res = client.updateRow(resp, req);

```

## Delete the data of a row \(DeleteRow\) { .section}

The DeleteRow interface is used to delete a row. No error is reported, regardless of whether this row exists or not.

**Example**

```language-cpp
DeleteRowRequest req;
{
    RowDeleteChange& chg = req.mutableRowChange();
    chg.mutableTable() = "YourTable";
    {
        // set primary key of the row to delete
        PrimaryKey& pkey = chg.mutablePrimaryKey();
        pkey.append() = PrimaryKeyColumn(
            "pkey",
            PrimaryKeyValue::toInteger(1));
    }
}
DeleteRowResponse resp;
Optional<OTSError> res = client.deleteRow(resp, req);

```

