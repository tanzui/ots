# Configure filter

Tablestore provides filter to filter the results on the server and return the rows that match the filter conditions. After results are filtered, only rows of data that matches the filter condition are returned.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Go SDK/Initialization.md).
-   A data table is created. Data is written to the table.

## Usage notes

When you query data by calling the GetRow, BatchGetRow, or GetRange operation, you can use the filter to return only the rows that meet the filter conditions.

Filters include SingleColumnValueFilter and CompositeColumnValueFilter.

-   SingleColumnValueFilter: determines whether to filter a row based only on the values of a reference column.
-   CompositeColumnValueFilter: determines whether to filter a row by combining the filter conditions of values of multiple reference columns.

## Limits

-   The filter conditions support relational operators such as =, !=, \>, \>=, <, and <=, and logical operators such as NOT, AND, and OR. You can specify a maximum of 10 conditions.
-   Reference columns in filters must be included in the read data. If the specified columns from which data is read do not include reference columns, the filter cannot query the values of reference columns.
-   You can use filters by calling the GetRow, BatchGetRow, and GetRange operations, which does not change the native semantics or limited items of these operations.

    When you use GetRange, the number of rows scanned cannot exceed 5,000, or the data scanned cannot exceed 4 MB in size.

    If the scanned 5,000 rows or 4 MB of data does not match the filter conditions, the rows in the response are empty. However, NextStartPrimaryKey may not be empty. In this case, you must use NextStartPrimaryKey to continue reading the data until NextStartPrimaryKey is empty.


## Parameters

|Parameter|Description|
|---------|-----------|
|ColumnName|The name of the reference column in the filter.|
|ColumnValue|The comparison value of the reference column in the filter.|
|ComparatorType|The relational operator in the filter. For more information, see [ComparatorType](/intl.en-US/API Reference/Data Types/ComparatorType.md).Relational operators include EQUAL \(=\), NOT\_EQUAL \(!=\), GREATER\_THAN \(\>\), GREATER\_EQUAL \(\>=\), LESS\_THAN \(<\), and LESS\_EQUAL \(<=\). In Tablestore, tablestore.CT\_EQUAL indicates EQUAL \(=\), tablestore.CT\_NOT\_EQUAL indicates NOT\_EQUAL \(!=\), tablestore.CT\_GREATER\_THAN indicates GREATER\_THAN \(\>\), tablestore.CT\_GREATER\_EQUAL indicates GREATER\_EQUAL \(\>=\), tablestore.CT\_LESS\_THAN indicates LESS\_THAN \(<\), and tablestore.CT\_LESS\_EQUAL indicates LESS\_EQUAL \(<=\). |
|LogicOperator|The logical operator in the filter. For more information, see [LogicalOperator](/intl.en-US/API Reference/Data Types/LogicalOperator.md).Logical operators include NOT, AND, and OR. In Tablestore, tablestore. LO\_NOT indicates NOT, tablestore.LO\_AND indicates AND, and tablestore.LO\_OR indicates OR. |
|FilterIfMissing|Specifies whether to return a row if the row does not contain the reference column. The type of this parameter value is Boolean. The default value is true, which indicates that if the reference column does not exist in a row, the row is returned.When FilterIfMissing is set to false, if the reference column does not exist in a row, the row is not returned. |
|LatestVersionOnly|Specifies whether to use only the value of the latest version for comparison when the reference column contains data of multiple versions. The type of this parameter value is Boolean. The default value is true, which indicates that if the reference column has multiple versions of data, only the value of the latest version is used for comparison.When LatestVersionsOnly is set to false, if a reference column has multiple versions of data, the value of all versions in the column is used for comparison. In this case, if only the value of one version meets the conditions, the row is returned. |

## Examples

-   The following code provides an example on how to create SingleColumnValueFilter:

    ```
    func GetRowWithFilter(client *tablestore.TableStoreClient, tableName string) {
        fmt.Println("begin to get row")
        pk := new(tablestore.PrimaryKey)
        pk.AddPrimaryKeyColumn("pk1", "pk1value1")
        pk.AddPrimaryKeyColumn("pk2", int64(2))
        pk.AddPrimaryKeyColumn("pk3", []byte("pk3"))
    
        // Specify that the rows are returned when the value of c1 is Zhejiang, and FilterIfMissing is true.
        condition := tablestore.NewSingleColumnCondition("c1", tablestore.ComparatorType(tablestore.CT_EQUAL), "Zhejiang")
        condition.FilterIfMissing = true
    
        criteria := &tablestore.SingleRowQueryCriteria{
            TableName:     tableName,
            PrimaryKey:    pk,
            MaxVersion:    1,
            Filter:        condition,
        }
    
        getResp, err := client.GetRow(&tablestore.GetRowRequest{SingleRowQueryCriteria: criteria})
        if err != nil {
            fmt.Println("getrow failed with error:", err)
        } else {
            colMap := getResp.GetColumnMap()
            fmt.Println("length is ", len(colMap.Columns))
            fmt.Println("get row col0 result is ", getResp.Columns[0].ColumnName, getResp.Columns[0].Value)
        }
    }
    ```

-   The following code provides an example on how to create CompositeColumnValueFilter:

    ```
    func GetRowWithCompositeColumnValueFilter(client *tablestore.TableStoreClient, tableName string) {
        fmt.Println("begin to get row")
        pk := new(tablestore.PrimaryKey)
        pk.AddPrimaryKeyColumn("pk1", "pk1value1")
        pk.AddPrimaryKeyColumn("pk2", int64(2))
        pk.AddPrimaryKeyColumn("pk3", []byte("pk3"))
    
        // Specify that the rows are returned when the value of c1 is Zhejiang, and the value of c2 is Hangzhou.
        filter := tablestore.NewCompositeColumnCondition(tablestore.LO_AND)
        filter1 := tablestore.NewSingleColumnCondition("c1", tablestore.CT_EQUAL, "Zhejiang")
        filter2 := tablestore.NewSingleColumnCondition("c2", tablestore.CT_EQUAL, "Hangzhou")
        filter.AddFilter(filter2)
        filter.AddFilter(filter1)
    
        criteria := &tablestore.SingleRowQueryCriteria{
            TableName:  tableName,
            PrimaryKey: pk,
            MaxVersion: 1,
            Filter:     filter,
        }
    
        getResp, err := client.GetRow(&tablestore.GetRowRequest{SingleRowQueryCriteria: criteria})
        if err != nil {
            fmt.Println("getrow failed with error:", err)
        } else {
            colMap := getResp.GetColumnMap()
            fmt.Println("length is ", len(colMap.Columns))
            fmt.Println("get row col0 result is ", getResp.Columns[0].ColumnName, getResp.Columns[0].Value)
        }
    }
    ```


