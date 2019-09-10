# Atomic counters {#concept_d45_tlc_dfb .concept}

Atomic counter is a new feature of Table Store that allows you to implement an atomic counter on an attribute. This feature provides statistics data for online applications such as keeping track of the number of page views \(PV\) on various topics.

In traditional database systems \(without atomic counters\), you must perform read, modify, and write \(RMW\) operations to increment an attribute value by one or other number. You must read the previous attribute value from a database, and modify it on a client. Finally, you write the modified value to the database. The consistency issue occurs in a database while multiple clients modify data at the same time.

Currently, you can fix this issue by starting a transaction to lock a row. Then you can perform RMW operations in this transaction. You can use a transaction to ensure consistent data in a row when multiple clients modify a single row. However, this solution reduces write performance of atomic counters. RMW operations will increase network overhead.

To deal with increasing overhead, atomic counters are used in Table Store. A transaction within a sequence of RMW operations is sent to a database as a request. The database performs the operations on a row by locking the row. To ensure data consistency, you can update atomic counters on a database server to improve write performance.

## Methods {#section_unq_vlc_dfb .section}

The following methods are added in the RowUpdateChange class to operate an atomic counter:

-   RowUpdateChange increment\(Column column\) is used to increment or decrement an attribute value by a number.
-   void addReturnColumn\(String columnName\) is used to specify the name of an atomic counter that will be returned.
-   void setReturnType\(ReturnType.RT\_AFTER\_MODIFY\) is used to specify a flag to indicate that the updated value of the atomic counter must be returned.

You can use RowUpdateChange to increment an atomic counter by a number as follows:

```
private static void incrementByUpdateRowApi(SyncClient client) {
        // You can specify a primary key.
        PrimaryKeyBuilder primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        primaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME, PrimaryKeyValue.fromString("pk0"));
        PrimaryKey primaryKey = primaryKeyBuilder.build();

        RowUpdateChange rowUpdateChange = new RowUpdateChange(TABLE_NAME, primaryKey);

        // You can increment the price value by 10 without specifying a timestamp.
        rowUpdateChange.increment(new Column("price", ColumnValue.fromLong(10)));

        // You can specify a flag to indicate that the updated value of the atomic counter must be returned.
        rowUpdateChange.addReturnColumn("price");
        rowUpdateChange.setReturnType(ReturnType.RT_AFTER_MODIFY);
        
        // You can update the price attribute.
        UpdateRowResponse response = client.updateRow(new UpdateRowRequest(rowUpdateChange));

        // You can display the updated value.
        Row row = result.getRow();
        System.out.println(row);
    }
```

**Note:** 

-   RowUpdateChange.addReturnColumn\(an attribute name\) is used to specify the name of an atomic counter that will be returned.
-   RowUpdateChange.setReturnType\(RT\_AFTER\_MODIFY is used to specify a flag to indicate that the updated value of the atomic counter must be returned.

## Scenarios {#section_f4q_vlc_dfb .section}

You can use an atomic counter to keep track of a row in real time. Assume that you create a table to store pictures. Each row in the table has a user ID. An attribute of the row is used to store pictures. Another attribute of the row is used as an atomic counter to count the number of pictures.

-   UpdateRow is used to add a picture to the table and increment the atomic counter by one.
-   UpdateRow is used to remove a picture from the table and decrement the atomic counter by one.
-   GetRow is used to read the value of the atomic counter to check the number of pictures.

This design ensures database consistency. When you add a picture to the table, the atomic counter is incremented by one instead of decremented by one.

## Restrictions {#section_h4q_vlc_dfb .section}

Note the following restrictions when using atomic counters:

-   Atomic counters only support the Integer type.
-   The default value of an empty atomic counter is zero. When you implement an atomic counter on an existing attribute with a non-Integer type, an OTSParameterInvalid error occurs.
-   You can update an atomic counter by using a positive or a negative number, but you must avoid an integer overflow. If an overflow issue appears, an OTSParameterInvalid error occurs.
-   When you modify an atomic counter, the value will not be returned by default. You can use addReturnColumn\(\) and setReturnType\(\) to specify the name and updated value of an atomic counter that will be returned.
-   You cannot update an attribute and an atomic counter simultaneously for a single request. If you have incremented or decremented the attribute A, then you cannot perform other operations, such as overwrite and delete operations on the attribute A.
-   You can perform multiple update operations on the same row using a BatchWriteRow request. When you perform an atomic counter operation on a row, other operations in this BatchWriteRow request cannot be performed on this row.
-   You can only implement an atomic counter on an attribute with the latest version. After you perform the update operation on the atomic counter, the atomic counter will be specified with a new version.
-   An error may occur when an atomic counter encounters network timeouts or system failures. You can retry the operation. An atomic counter may be updated twice. This symptom leads to an overcounting or undercounting issue. In this case, we recommend that you can use [conditional update](../../../../intl.en-US/SDK Reference/Java SDK/Condition update.md#) to precisely update the attribute.

