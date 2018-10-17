# Single-row operations {#concept_43013_zh .concept}

Table Store provides the following single-row operation APIs: PutRow, GetRow, UpdateRow, and DeleteRow.

## PutRow {#section_owq_rg2_2fb .section}

The PutRow API is used to insert a row of data. If this row already exists, it is overwritten.

When performing PutRow, you can use the Conditional Update function to set conditions for the existence of the row to be written or values in certain columns of the existing row. For more information, see [Conditional Update](reseller.en-US/SDK Reference/Java SDK/Condition update.md#).

**Example 1**

In this example, a row with 10 attribute columns is written, and one version for each column is entered. The version number \(timestamp\) is specified by the server.

```language-java
private static void putRow(SyncClient client, String pkValue) {
    // Build primary key.
    PrimaryKeyBuilder primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
    primaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME, PrimaryKeyValue.fromString(pkValue));
    PrimaryKey primaryKey = primaryKeyBuilder.build();

    RowPutChange rowPutChange = new RowPutChange(TABLE_NAME, primaryKey);

    //Add attribute columns
    for (int i = 0; i < 10; i++) {
        rowPutChange.addColumn(new Column("Col" + i, ColumnValue.fromLong(i)));
    }

    client.putRow(new PutRowRequest(rowPutChange));
}

```

**Example 2**

In this example, a row with 10 attribute columns is written, and three versions for each column is entered. The version number \(timestamp\) is specified by the client.

```language-java
private static void putRow(SyncClient client, String pkValue) {
    // Build primary key.
    PrimaryKeyBuilder primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
    primaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME, PrimaryKeyValue.fromString(pkValue));
    PrimaryKey primaryKey = primaryKeyBuilder.build();

    RowPutChange rowPutChange = new RowPutChange(TABLE_NAME, primaryKey);

    //Add attribute columns
    long ts = System.currentTimeMillis();
    for (int i = 0; i < 10; i++) {
        for (int j = 0; j < 3; j++) {
            rowPutChange.addColumn(new Column("Col" + i, ColumnValue.fromLong(j), ts + j));
        }
    }

    client.putRow(new PutRowRequest(rowPutChange));
}

```

**Example 3**

In this example, if no row exists, the data is written.

```language-java
private static void putRow(SyncClient client, String pkValue) {
    // Build primary key.
    PrimaryKeyBuilder primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
    primaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME, PrimaryKeyValue.fromString(pkValue));
    PrimaryKey primaryKey = primaryKeyBuilder.build();

    RowPutChange rowPutChange = new RowPutChange(TABLE_NAME, primaryKey);

    // Expect the original row does not exist
    rowPutChange.setCondition(new Condition(RowExistenceExpectation.EXPECT_NOT_EXIST));

    //Add attribute columns
    long ts = System.currentTimeMillis();
    for (int i = 0; i < 10; i++) {
        for (int j = 0; j < 3; j++) {
            rowPutChange.addColumn(new Column("Col" + i, ColumnValue.fromLong(j), ts + j));
        }
    }

    client.putRow(new PutRowRequest(rowPutChange));
}

```

**Example 4**

In this example, the original row is expected to exist and to write data when the Col0 value is greater than 100.

```language-java
private static void putRow(SyncClient client, String pkValue) {
    // Build primary key.
    PrimaryKeyBuilder primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
    primaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME, PrimaryKeyValue.fromString(pkValue));
    PrimaryKey primaryKey = primaryKeyBuilder.build();

    RowPutChange rowPutChange = new RowPutChange(TABLE_NAME, primaryKey);

    // Expect the original row to exist and write data when the Col0 value is greater than 100
    Condition condition = new Condition(RowExistenceExpectation.EXPECT_EXIST);
    condition.setColumnCondition(new SingleColumnValueCondition("Col0",
            SingleColumnValueCondition.CompareOperator.GREATER_THAN, ColumnValue.fromLong(100)));
    rowPutChange.setCondition(condition);

    //Add attribute columns
    long ts = System.currentTimeMillis();
    for (int i = 0; i < 10; i++) {
        for (int j = 0; j < 3; j++) {
            rowPutChange.addColumn(new Column("Col" + i, ColumnValue.fromLong(j), ts + j));
        }
    }

    client.putRow(new PutRowRequest(rowPutChange));
}

```

## GetRow { .section}

The single-row GetRow API is used to read a single row of data. It has the following parameters:

-   PrimaryKey: The primary key of the row to read. It is a required parameter.

-   ColumnsToGet: The set of columns to read. If not set, all columns are read.

-   MaxVersions: The maximum number of versions to read. At least one of the MaxVersions and TimeRange parameters must be set.

-   TimeRange: The range of version numbers to read. At least one of the MaxVersions and TimeRange parameters must be set.

-   Filter: The filters applied. Filters are used by the server to filter the read results again.


**Example 1**

In this example, the latest version of single row is read and the specific ColumnsToGet is set.

```language-java
        private static void getRow(SyncClient client, String pkValue) {
            // Build primary key.
            PrimaryKeyBuilder primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
            primaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME, PrimaryKeyValue.fromString(pkValue));
            PrimaryKey primaryKey = primaryKeyBuilder.build();

            // Read a row.
            SingleRowQueryCriteria criteria = new SingleRowQueryCriteria(TABLE_NAME, primaryKey);
            // Set the latest version to be read.
            criteria.setMaxVersions(1);
            GetRowResponse getRowResponse = client.getRow(new GetRowRequest(criteria));
            Row row = getRowResponse.getRow();

            System.out.println("Read complete, result:");
            System.out.println(row);

            // Set the columns to read
            criteria.addColumnsToGet("Col0");
            getRowResponse = client.getRow(new GetRowRequest(criteria));
            row = getRowResponse.getRow();

            System.out.println("Read complete, result:");
            System.out.println(row);
        }

```

**Example 2**

In this example, a filter is set.

```language-java
	    private static void getRow(SyncClient client, String pkValue) {
            // Build primary key.
            PrimaryKeyBuilder primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
            primaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME, PrimaryKeyValue.fromString(pkValue));
            PrimaryKey primaryKey = primaryKeyBuilder.build();
    
            // Read a row.
            SingleRowQueryCriteria criteria = new SingleRowQueryCriteria(TABLE_NAME, primaryKey);
            // Set the latest version to be read.
            criteria.setMaxVersions(1);
    
            // Set the filter; when the Col0 value is 0, this row is returned.
            SingleColumnValueFilter singleColumnValueFilter = new SingleColumnValueFilter("Col0",
                    SingleColumnValueFilter.CompareOperator.EQUAL, ColumnValue.fromLong(0));
            // If the column Col0 does not exist, the data will not be returned.
            singleColumnValueFilter.setPassIfMissing(false);
            criteria.setFilter(singleColumnValueFilter);
    
            GetRowResponse getRowResponse = client.getRow(new GetRowRequest(criteria));
            Row row = getRowResponse.getRow();
    
            System.out.println("Read complete, result:");
            System.out.println(row);
        }

```

## UpdateRow { .section}

The UpdateRow API is used to update a single row of data. If no row exists, a new row is added.

The update operation includes write column, delete column, and delete column version operations.

When performing UpdateRow, you can use the Conditional Update function to set conditions for the existence of the row to be updated or values in certain columns of the existing row. For more information, see [Conditional Update](reseller.en-US/SDK Reference/Java SDK/Condition update.md#).

**Example 1**

In this example, several columns are updated, while a specified version of a specified column, and a specified column, are deleted.

```language-java
        private static void updateRow(SyncClient client, String pkValue) {
            // Build primary key.
            PrimaryKeyBuilder primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
            primaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME, PrimaryKeyValue.fromString(pkValue));
            PrimaryKey primaryKey = primaryKeyBuilder.build();
    
            RowUpdateChange rowUpdateChange = new RowUpdateChange(TABLE_NAME, primaryKey);
    
            // Update columns
            for (int i = 0; i < 10; i++) {
                rowUpdateChange.put(new Column("Col" + i, ColumnValue.fromLong(i)));
            }
    
            // Delete the specified version of the specified column
            rowUpdateChange.deleteColumn("Col10", 1465373223000L);
    
            // Delete specified column
            rowUpdateChange.deleteColumns("Col11");
    
            client.updateRow(new UpdateRowRequest(rowUpdateChange));
        }
        

```

**Example 2**

In this example, update conditions are set.

```language-java
	    private static void updateRow(SyncClient client, String pkValue) {
            // Build primary key.
            PrimaryKeyBuilder primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
            primaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME, PrimaryKeyValue.fromString(pkValue));
            PrimaryKey primaryKey = primaryKeyBuilder.build();

            RowUpdateChange rowUpdateChange = new RowUpdateChange(TABLE_NAME, primaryKey);

            // Expect the original row to exist and update data when the Col0 value is greater than 100
            Condition condition = new Condition(RowExistenceExpectation.EXPECT_EXIST);
            condition.setColumnCondition(new SingleColumnValueCondition("Col0",
                    SingleColumnValueCondition.CompareOperator.GREATER_THAN, ColumnValue.fromLong(100)));
            rowUpdateChange.setCondition(condition);

            // Update columns
            for (int i = 0; i < 10; i++) {
                rowUpdateChange.put(new Column("Col" + i, ColumnValue.fromLong(i)));
            }

            // Delete the specified version of the specified column
            rowUpdateChange.deleteColumn("Col10", 1465373223000L);

            // Delete specified column
            rowUpdateChange.deleteColumns("Col11");

            client.updateRow(new UpdateRowRequest(rowUpdateChange));
        }

```

## DeleteRow { .section}

The DeleteRow API is used to delete a single row.

When performing DeleteRow, you can use the Conditional Update function to set conditions for the existence of the row to be deleted or values in certain columns of the existing row. For more information, see [Conditional Update](reseller.en-US/SDK Reference/Java SDK/Condition update.md#).

**Example 1**

In this example, a row is deleted.

```language-java
        private static void deleteRow(SyncClient client, String pkValue) {
            // Build primary key.
            PrimaryKeyBuilder primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
            primaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME, PrimaryKeyValue.fromString(pkValue));
            PrimaryKey primaryKey = primaryKeyBuilder.build();
    
            RowDeleteChange rowDeleteChange = new RowDeleteChange(TABLE_NAME, primaryKey);
    
            client.deleteRow(new DeleteRowRequest(rowDeleteChange));
        }

```

**Example 2**

In this example, deletion conditions are set.

```language-java
        private static void deleteRow(SyncClient client, String pkValue) {
            // Build primary key.
            PrimaryKeyBuilder primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
            primaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME, PrimaryKeyValue.fromString(pkValue));
            PrimaryKey primaryKey = primaryKeyBuilder.build();

            RowPutChange rowPutChange = new RowPutChange(TABLE_NAME, primaryKey);

            // Expect the original row to exist and delete data when the Col0 value is greater than 100
            Condition condition = new Condition(RowExistenceExpectation.EXPECT_EXIST);
            condition.setColumnCondition(new SingleColumnValueCondition("Col0",
                    SingleColumnValueCondition.CompareOperator.GREATER_THAN, ColumnValue.fromLong(100)));
            rowDeleteChange.setCondition(condition);

            client.deleteRow(new DeleteRowRequest(rowDeleteChange));
        }

```

