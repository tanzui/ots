# Configure filter

Tablestore provides filter to filter the results on the server and return the rows that match the filter conditions. After results are filtered, only rows of data that matches the filter condition are returned.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/NodeJS SDK/Initialization.md).
-   A data table is created. Data is written to the table.

## Usage notes

When you query data by calling the GetRow, BatchGetRow, or GetRange operation, you can use the filter to return only the rows that meet the filter conditions.

Filters include SingleColumnCondition and CompositeColumnCondition.

-   SingleColumnCondition: determines whether to filter a row based only on the values of a reference column.
-   CompositeColumnCondition: determines whether to filter a row by combining the filter conditions of values of multiple reference columns.

## Limits

-   The filter conditions support relational operators such as =, !=, \>, \>=, <, and <=, and logical operators such as NOT, AND, and OR. You can specify a maximum of 10 conditions.
-   Reference columns in filters must be included in the read result. If the specified columns from which data is read do not include reference columns, the filter cannot query the values of reference columns.
-   You can use filters by calling the GetRow, BatchGetRow, and GetRange operations, which does not change the native semantics or limited items of these operations.

    When you use GetRange, the number of rows scanned cannot exceed 5,000, or the data scanned cannot exceed 4 MB in size.

    If the scanned 5,000 rows or 4 MB does not match the filter conditions, the rows in the response are empty. However, next\_start\_primary\_key may not be empty. In this case, you need to use next\_start\_primary\_key to continue reading the data until next\_start\_primary\_key is empty.


## Parameters

|Parameter|Description|
|---------|-----------|
|columnName|The name of the reference column in the filter.|
|columnValue|The comparison value of the reference column in the filter.|
|ComparatorType|The relational operator in the filter. For more information, see [ComparatorType](/intl.en-US/API Reference/Data Types/ComparatorType.md).Relational operators include EQUAL \(=\), NOT\_EQUAL \(!=\), GREATER\_THAN \(\>\), GREATER\_EQUAL \(\>=\), LESS\_THAN \(<\), and LESS\_EQUAL \(<=\). |
|LogicOperator|The logical operator in the filter. For more information, see [LogicalOperator](/intl.en-US/API Reference/Data Types/LogicalOperator.md).Logical operators include NOT, AND, and OR. |
|passIfMissing|Specifies whether to return a row if the row does not contain the reference column. The type of this parameter value is Boolean. The default value is true, which indicates that if the reference column does not exist in a row, the row is returned.When PassIfMissing is set to false, if the reference column does not exist in a row, the row is not returned. |
|latestVersionOnly|Specifies whether to use only the value of the latest version for comparison when the reference column contains data of multiple versions. The type of this parameter value is Boolean. The default value is true, which indicates that if the reference column has multiple versions of data, only the value of the latest version is used for comparison.When latestVersionOnly is set to false, if a reference column has multiple versions of data, the value of all versions in the column is used for comparison. In this case, if only the value of one version meets the conditions, the row is returned. |

## Examples

-   The following code provides an example on how to create SingleColumnCondition:

    ```
    function getRowWithCondition() {
      // Specify that the row is returned when col1 is Tablestore. When passIfMissing is set to true, the row is returned if this column does not exist. When passIfMissing is set to false, the row is not returned if this column does not exist.
      var condition = new TableStore.SingleColumnCondition('col1', 'Tablestore', TableStore.ComparatorType.EQUAL,true);
    
      params.columnFilter = condition;
      client.getRow(params, function (err, data) {
        if (err) {
          console.log('error:', err);
          return;
        }
        console.log('success:', data);
      });
    }
    ```

-   The following code provides an example on how to create CompositeCondition:

    ```
    function getRowWithCompositeCondition() {
      // Specify that the row is returned when col1 is Tablestore, and the value of col5 is 123456789.
      var condition = new TableStore.CompositeCondition(TableStore.LogicalOperator.AND);
      condition.addSubCondition(new TableStore.SingleColumnCondition('col1', 'Tablestore', TableStore.ComparatorType.EQUAL));
      condition.addSubCondition(new TableStore.SingleColumnCondition('col5', Long.fromNumber(123456789), TableStore.ComparatorType.EQUAL));
    
      params.columnFilter = condition;
      client.getRow(params, function (err, data) {
        if (err) {
          console.log('error:', err);
          return;
        }
        console.log('success:', data);
      });
    }
    ```


