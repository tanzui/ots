# Condition update {#concept_43026_zh .concept}

The conditional update function updates table data only when specified conditions are met. If the conditions are not met, the update fails. Conditional update is applicable to PutRow, UpdateRow, DeleteRow, and BatchWriteRow.

Judgment conditions include the row existence condition and column-based condition.

-   Row existence condition: This condition is divided into three types, IGNORE, EXPECT\_EXIST, and EXPECT\_NOT\_EXIST. When a table needs to be updated, the system first checks the row existence condition. If the row existence condition is not met, the update fails and the system throws an error.

-   Column-based condition: This condition currently supports SingleColumnValueCondition and CompositeColumnValueCondition. These are used to make condition-based judgments based on the value\(s\) of one or more columns, similar to the conditions used by the Table Store filters.


Conditional update can be used to implement optimistic locking. When a row needs to be updated, the system first obtains the value of a column. For example, we assume Column A has a value of 1. Set the condition `Column A = 1`, update the row, and set `Column A = 2`. If the update fails, this means the row has been updated by another client.

**Example 1**

Construct a SingleColumnValueCondition.

```language-java
		// Set the condition: Col0==0.
        SingleColumnValueCondition singleColumnValueCondition = new SingleColumnValueCondition("Col0",
                SingleColumnValueCondition.CompareOperator.EQUAL, ColumnValue.fromLong(0));
        // If the column Col0 does not exist, the condition is not met.
        singleColumnValueCondition.setPassIfMissing(false);
        // Only judge the latest version.
        singleColumnValueCondition.setLatestVersionsOnly(true);

```

**Example 2**

Construct a CompositeColumnValueCondition.

```
// The condition composite1 is: (Col0 == 0) AND (Col1 > 100).
        CompositeColumnValueCondition composite1 = new CompositeColumnValueCondition(CompositeColumnValueCondition.LogicOperator.AND);
        SingleColumnValueCondition single1 = new SingleColumnValueCondition("Col0",
                SingleColumnValueCondition.CompareOperator.EQUAL, ColumnValue.fromLong(0));
        SingleColumnValueCondition single2 = new SingleColumnValueCondition("Col1",
                SingleColumnValueCondition.CompareOperator.GREATER_THAN, ColumnValue.fromLong(100));
        composite1.addCondition(single1);
        composite1.addCondition(single2);
        // The condition composite2 is: ( (Col0 == 0) AND (Col1 > 100) ) OR (Col2 <= 10).
        CompositeColumnValueCondition composite2 = new CompositeColumnValueCondition(CompositeColumnValueCondition.LogicOperator.OR);
        SingleColumnValueCondition single3 = new SingleColumnValueCondition("Col2",
                SingleColumnValueCondition.CompareOperator.LESS_EQUAL, ColumnValue.fromLong(10));
        composite2.addCondition(composite1);
        composite2.addCondition(single3);
```

**Example 3**

You can use Condition to implement the optimistic locking feature. The following example shows how to increment a column using Condition.

```language-java
        private static void updateRowWithCondition(SyncClient client, String pkValue) {
            // Build primary key.
            PrimaryKeyBuilder primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
            primaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME, PrimaryKeyValue.fromString(pkValue));
            PrimaryKey primaryKey = primaryKeyBuilder.build();
    
            // Read a row.
            SingleRowQueryCriteria criteria = new SingleRowQueryCriteria(TABLE_NAME, primaryKey);
            criteria.setMaxVersions(1);
            GetRowResponse getRowResponse = client.getRow(new GetRowRequest(criteria));
            Row row = getRowResponse.getRow();
            long col0Value = row.getLatestColumn("Col0").getValue().asLong();
    
            // Set conditional update for Col0, and use column value+1.
            RowUpdateChange rowUpdateChange = new RowUpdateChange(TABLE_NAME, primaryKey);
            Condition condition = new Condition(RowExistenceExpectation.EXPECT_EXIST);
            ColumnCondition columnCondition = new SingleColumnValueCondition("Col0", SingleColumnValueCondition.CompareOperator.EQUAL, ColumnValue.fromLong(col0Value));
            condition.setColumnCondition(columnCondition);
            rowUpdateChange.setCondition(condition);
            rowUpdateChange.put(new Column("Col0", ColumnValue.fromLong(col0Value + 1)));
    
            try {
                client.updateRow(new UpdateRowRequest(rowUpdateChange));
            } catch (TableStoreException ex) {
                System.out.println(ex.toString());
            }
        }

```

