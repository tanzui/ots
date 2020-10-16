# Configure atomic counter

Atomic counters allow you to implement an atomic counter on a column. This feature provides statistic data for online applications such as the number of page views \(PVs\) on various topics.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/NodeJS SDK/Initialization.md).
-   A data table is created. Data is written to the table.

## Limits

-   You can implement atomic counters only on INTEGER columns.
-   By default, if a column that is specified as an atomic counter does not exist, the value of the column is 0 before you write data. If a column that is specified as an atomic counter is not an INTEGER column, an OTSParameterInvalid error occurs.
-   You can update an atomic counter by using a positive or negative number, but you must avoid an integer overflow. If an integer overflow occurs, an OTSParameterInvalid error is returned.
-   By default, the value of an atomic counter is not returned in the response to an update row request. You can specify that the increased value of an atomic counter is returned.
-   You cannot specify a column as an atomic counter and update the column in a single request. If Attribute Column A is set to an atomic counter, you cannot perform other operations such as overwrite and delete operations on the attribute column A.
-   You can perform multiple update operations on the same row by using a BatchWriteRow request. However, if you perform an atomic counter operation on a row, you can perform only one update operation on the row in a BatchWriteRow request.
-   Only the value of the latest version of an atomic counter is increased. You cannot increase the value of a specified version of an atomic counter. After you update a row, a new version of data is inserted to the atomic counter in the row.

## API operations

The API operations to implement the atomic counter feature are added to the updateRow operation. The following table lists these operations.

|Operation|Description|
|---------|-----------|
|updateOfAttributeColumns|Increases or decreases a value by a number. The update type is INCREMENT.|
|returnContent|Specifies the column name and return type on which operations are performed to implement atomic counter. The updated value in the column on which operations are performed to implement atomic counter must be returned.|

## Parameters

|Parameter|Description|
|---------|-----------|
|tableName|The name of the table.|
|columnName|The name of the column on which operations are performed to implement atomic counter. Columns whose valid values are integers are supported to implement the atomic counter feature.|
|value|The value to update the columns on which operations are performed to implement atomic counter.|
|returnColumns|Specifies the column names whose value needs to be returned for the columns on which operations are performed to implement atomic counter.|
|returnType|Sets the return type to TableStore.ReturnType.AfterModify and returns the values of the columns on which operations are performed to implement atomic counter.|

## Examples

The following code provides an example on how to call the updateRow operation to update the columns whose valid values are integers when you write a row of data. Set the type to INCREMENT for the attribute column:

```
var params = {
  tableName: "<Your-Table-Name>",
  condition: new TableStore.Condition(TableStore.RowExistenceExpectation.EXPECT_EXIST, null),
  primaryKey: [{'pk0': Long.fromNumber(1)}],
  // Specify that the value of the price column on which operations are performed to implement atomic counter is increased by 10. Do not set timestamps.
  updateOfAttributeColumns: [
    {'INCREMENT': [{'price': Long.fromNumber(10)}]}
  ],
  // Set the return type to TableStore.ReturnType.AfterModify and return the values of the columns on which operations are performed to implement atomic counter.
  returnContent: {
    returnColumns: ["price"],
    returnType: TableStore.ReturnType.AfterModify
  }
};

client.updateRow(params,
  function (err, data) {
    if (err) {
      console.log('error:', err);
      return;
    }

    console.log('success:', JSON.stringify(data, null, 2));
  });
```

