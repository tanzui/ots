# Conditional update {#concept_35194_zh .concept}

A conditional update is an update of table data that executes only when specified conditions are met. A conditional update can be based on a combination of up to 10 conditions. Supported conditions include arithmetic operations \(=, ! =, \>, \>=, <, and <=\) and logical operations \(NOT, AND, and OR\). The conditional update is applicable to [PutRow](../../../../reseller.en-US/API Reference/Operations/PutRow.md#), [UpdateRow](../../../../reseller.en-US/API Reference/Operations/UpdateRow.md#), [DeleteRow](../../../../reseller.en-US/API Reference/Operations/DeleteRow.md#), and [BatchWriteRow](../../../../reseller.en-US/API Reference/Operations/BatchWriteRow.md#).

The column-based judgment conditions include the row existence condition and column-based condition.

-   The [Row existence condition](../../../../reseller.en-US/API Reference/Data Types/Condition.md#) is classified into `IGNORE`, `EXPECT_EXIST`, and `EXPECT_NOT_EXIST`. When a table needs to be updated, the system first checks the row existence condition. If the row existence condition is not met, an error occurs during the update.

-   The column-based condition supports `SingleColumnValueCondition` and `CompositeColumnValueCondition`, which are used to perform the condition-based judgment based on the values of a column or certain columns, similar to the conditions used by the Table Store filters.


Conditional update also supports optimistic locking strategy. That is, when a row needs to be updated, the system first obtains the value of a column. For example, the value of Column A is 1, and its condition is set as `Column A = 1`. Set `Column A = 2`, then update the row. If a failure occurs during the update, it means that the row has been successfully updated by another client.

**Note:** In highly concurrent applications such as webpage view counting or gaming \(where atomic counter updates are required\), the probability of failed conditional updates is high. If this occurs, we recommend that you retry the update until successful.

## Procedure {#section_wyt_4pg_cfb .section}

1.  Construct SingleColumnValueCondition.

    ```
     // set condition  Col0==0.
     SingleColumnValueCondition singleColumnValueCondition = new SingleColumnValueCondition("Col0",
             SingleColumnValueCondition.CompareOperator.EQUAL, ColumnValue.fromLong(0));
     // If column Col0 does not exist, the condition check fails.
     singleColumnValueCondition.setPassIfMissing(false);
     // Only check the latest version
     singleColumnValueCondition.setLatestVersionsOnly(true);
    
    ```

2.  Construct CompositeColumnValueCondition.

    ```
     // condition composite1 is (Col0 == 0) AND (Col1 > 100)
     CompositeColumnValueCondition composite1 = new CompositeColumnValueCondition(CompositeColumnValueCondition.LogicOperator.AND);
     SingleColumnValueCondition single1 = new SingleColumnValueCondition("Col0",
             SingleColumnValueCondition.CompareOperator.EQUAL, ColumnValue.fromLong(0));
     SingleColumnValueCondition single2 = new SingleColumnValueCondition("Col1",
             SingleColumnValueCondition.CompareOperator.GREATER_THAN, ColumnValue.fromLong(100));
     composite1.addCondition(single1);
     composite1.addCondition(single2);
    
     // condition composite2 is ( (Col0 == 0) AND (Col1 > 100) ) OR (Col2 <= 10)
     CompositeColumnValueCondition composite2 = new CompositeColumnValueCondition(CompositeColumnValueCondition.LogicOperator.OR);
     SingleColumnValueCondition single3 = new SingleColumnValueCondition("Col2",
             SingleColumnValueCondition.CompareOperator.LESS_EQUAL, ColumnValue.fromLong(10));
     composite2.addCondition(composite1);
     composite2.addCondition(single3);     
    
    ```

3.  Implement an increasing column by the optimistic locking strategy based on the conditional update.

    ```
     private static void updateRowWithCondition(SyncClient client, String pkValue) {
         // construct the primary
         PrimaryKeyBuilder primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
         primaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME, PrimaryKeyValue.fromString(pkValue));
         PrimaryKey primaryKey = primaryKeyBuilder.build();
    
         // read a row
         SingleRowQueryCriteria criteria = new SingleRowQueryCriteria(TABLE_NAME, primaryKey);
         criteria.setMaxVersions(1);
         GetRowResponse getRowResponse = client.getRow(new GetRowRequest(criteria));
         Row row = getRowResponse.getRow();
         long col0Value = row.getLatestColumn("Col0").getValue().asLong();
    
         // Col0 = Col0 + 1 by conditional update
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


## Example { .section}

The following operations are examples of updates that are executed for highly concurrent applications:

```
	// Get the old value
    old_value = Read();
	// compute such as increment 1
    new_value = func(old_value);
	// Update by the new value
    Update(new_value)；

```

The conditional update makes sure Update \(new\_value\) if value equals to old\_value in a highly concurrent environment where old\_value may be updated by another client.

## Billing { .section}

Writing or updating data successfully does not affect the capacity unit \(CU\) calculation rules of the interfaces. However, if the conditional update fails, one unit of write CU and one unit of read CU are consumed, which are billable.

