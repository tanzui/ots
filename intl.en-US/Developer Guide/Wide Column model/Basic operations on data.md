# Basic operations on data

This topic describes the basic operations that you can perform on data in Tablestore, including single-row operations and multi-row operations. You can call these basic operations to write and read data in tables.

## API operations

A row is the basic unit of a table. Rows consist of primary keys and attributes. A primary key is required for a row. Rows within a table contain primary key columns of the same names and same data types. Attributes are optional for a row. Rows within a table can contain different attributes. For more information, see [Overview](/intl.en-US/Developer Guide/Wide Column model/Overview.md).

Tablestore provides two types of data operations, as described in the following table.

|Type|API operation|Description|
|----|-------------|-----------|
|Single-row operations|GetRow|You can call this operation to read data from a row.|
|PutRow|You can call this operation to insert a row into a table. If the row exists, data in the row is deleted and new data is inserted.|
|UpdateRow|You can call this operation to update a specified row of a table. You can add attribute columns to or delete attribute columns from a row. You can also update the values of attribute columns in a row. If the row you want to update does not exist, a new row is inserted into the table. However, if an UpdateRow operation only deletes specified columns from a row and the row does not exist, no row is inserted into the table. |
|DeleteRow|You can call this operation to delete a specified row from a table. If the specified row does not exist, no change is made to the table. |
|Multi-row operations|BatchGetRow|You can call this operation to read several rows of data from one or more tables.|
|BatchWriteRow|You can call this operation to insert rows into or delete rows from one or more tables. You can also call this operation to update rows in one or more tables.|
|GetRange|You can call this operation to read data within the specified range based on the primary key values.|

## Single-row operations

-   Single-row write operations

    Single-row write operations include PutRow, UpdateRow, and DeleteRow. The responses are different for different operation results.

    -   If an operation succeeds, Tablestore returns the capacity units \(CUs\) consumed by the operation.

        The following table describes the rules used to calculate the number of CUs consumed by a single-row write operation.

        **Note:** Write operations consume read CUs based on the conditions that you specify.

        |API operation|Rule to calculate CUs|
        |-------------|---------------------|
        |[PutRow](/intl.en-US/API Reference/Operations/PutRow.md)|The number of read and write CUs consumed by a PutRow operation is calculated based on the following rules:         -   The number of consumed write CUs is calculated based on the formula: Number of consumed write CUs = \[\(Size of primary key columns in the row + Size of attribute columns in the row\)/4 KB\] rounded up.
        -   If the value of the condition field is not IGNORE, the PutRow operation consumes read CUs. The number of consumed read CUs is calculated based on the formula: Number of consumed read CUs = \(Size of primary key columns in the row/4 KB\) rounded up.
        -   If the row existence condition is not met, the operation fails, and one write and one read CU are consumed. |
        |[UpdateRow](/intl.en-US/API Reference/Operations/UpdateRow.md)|The number of read and write CUs consumed by an UpdateRow operation is calculated based on the following rules:         -   The number of consumed write CUs is calculated based on the formula: Number of consumed write CUs = \[\(Size of primary key columns in the row + Size of attribute columns to update in the row\)/4 KB\] rounded up.

If the UpdateRow request contains deletion instructions on specified attribute columns, the length of the name of each attribute column to delete is calculated as the column size.

        -   If the value of the condition field is not IGNORE, the PutRow operation consumes read CUs. The number of consumed read CUs is calculated based on the formula: Number of consumed read CUs = \(Size of primary key columns in the row/4 KB\) rounded up.
        -   If the row existence condition is not met, the operation fails, and one write and one read CU are consumed. |
        |[DeleteRow](/intl.en-US/API Reference/Operations/DeleteRow.md)|The number of read and write CUs consumed by a DeleteRow operation is calculated based on the following rules:         -   The number of consumed write CUs is calculated based on the formula: Number of consumed write CUs = \(Size of primary key columns in the row/4 KB\) rounded up.
        -   If the value of the condition field is not IGNORE, the PutRow operation consumes read CUs. The number of consumed read CUs is calculated based on the formula: Number of consumed read CUs = \(Size of primary key columns in the row/4 KB\) rounded up.
        -   If the row existence condition is not met, the operation fails, and one write CU is consumed. |

    -   If errors occur, such as parameter check failures, excessive data in a row, or existence check failures, Tablestore returns the corresponding error codes.
    By specifying the condition field in the write request of a single row, you can specify whether to perform row existence check before the write operation. You can set the condition field to one of the following values:

    |condition|Description|
    |---------|-----------|
    |IGNORE|The row existence check is not performed.|
    |EXPECT\_EXIST|The row is expected to exist.     -   If the row exists, the operation result is not affected.
    -   If the row does not exist, the operation fails. |
    |EXPECT\_NOT\_EXIST|The row is expected not to exist.     -   If the row does not exist, the operation result is not affected.
    -   If the row exists, the operation fails.
**Note:** If you set condition to EXPECT\_NOT\_EXIST in a DeleteRow or UpdateRow operation, the operation is unnecessary because to delete a row that does not exist is unnecessary. To update a row that does not exist, you can use the PutRow operation. |

    Parameters

    The following table describes the parameters that can be configured for a single-row write operation.

    |Parameter|Description|
    |---------|-----------|
    |primary\_keys|The names and values of primary key columns.|
    |attributes|The names and values of attribute columns.|

    Examples

    The following examples show how to calculate the write and read CUs consumed by a single-row write operation:

    -   Example 1

        Use PutRow to insert a row into a table.

        The number of write and read CUs consumed by the PutRow operation:

        -   If you set condition to EXPECT\_EXIST, the number of consumed write and read CUs is calculated based on the formulas: Number of consumed write CUs = \(4322 bytes/4 KB\) rounded up and Number of consumed read CUs = \(10 bytes/4 KB\) rounded up. \(The size of the primary key columns in the row is 10 bytes.\) In this case, the PutRow operation consumes two write CUs and one read CU.
        -   If you set condition to IGNORE, the number of consumed read CUs is zero, and the number of consumed write CUs is calculated based on the formula: Number of consumed write CUs = \(4322 bytes/4 KB\) rounded up. In this case, the PutRow operation consumes two write CUs and zero read CUs.
        -   If you set condition to EXPECT\_NOT\_EXIST, the row existence check condition is not met and the PutRow operation fails. Therefore, the PutRow operation consumes one write CU and one read CU.
        ```
        // The PutRow operation
        //row_size=len('pk')+len('value1')+len('value2')+8Byte+1300Byte+3000Byte=4322Byte
        {
            primary_keys:{'pk':1},
            attributes:{'value1':String(1300Byte), 'value2':String(3000Byte)}
        }
        
        // The existing row
        //row_size=len('pk')+len('value2')+8Byte+900Byte=916Byte
        //row_primarykey_size=len('pk')+8Byte=10Byte
        {
            primary_keys:{'pk':1},
            attributes:{'value2':String(900Byte)}
        }
                                    
        ```

    -   Example 2

        Use UpdateRow to update a row.

        The number of write and read CUs consumed by the UpdateRow operation:

        -   If you set condition to IGNORE, the number of consumed read CU is zero， and the number of consumed write CUs is calculated based on the formula: Number of consumed write CUs = \(922 bytes/4 KB\) rounded up. In this case, the UpdateRow operation consumes one write CU and zero read CUs.
        -   If you set condition to EXPECT\_EXIST, the row existence check condition is not met and the UpdateRow operation fails. In this case, the PutRow operation consumes one write CU and one read CU.
        ```
        // The UpdateRow operation
        // The row size includes the length of the deleted column names.
        //row_size=len('pk')+len('value1')+len('value2')+8Byte+900Byte=922Byte
        {
            primary_keys:{'pk':1},
            attributes:{'value1':String(900Byte), 'value2':Delete}
        }
        
        // The specified row does not exist.
        //row_size=0
                                    
        ```

    -   Example 3

        Use UpdateRow to update an existing row.

        The number of write and read CUs consumed by the UpdateRow operation:

        -   If you set condition to EXPECT\_EXIST, the number of consumed write and read CUs is calculated based on the formulas: Number of consumed write CUs = \(4322 bytes/4 KB\) rounded up and Number of consumed read CUs = \(10 bytes/4 KB\) rounded up. \(The size of the primary key columns in the row is 10 bytes.\) In this case, the UpdateRow operation consumes two write CUs and one read CU.
        -   If you set condition to IGNORE, the number of consumed read CUs is zero, and the number of consumed write CUs is calculated based on the formula: Number of consumed write CUs = \(4322 bytes/4 KB\) rounded up. In this case, the UpdateRow operation consumes two write CUs and zero read CUs.
        ```
        // The UpdateRow operation
        //row_size=len('pk')+len('value1')+len('value2')+8 Byte+1300 Byte+3000 Byte=4322 Byte
        {
            primary_keys:{'pk':1},
            attributes:{'value1':String(1300 Byte), 'value2':String(3000 Byte)}
        }
        // The existing row
        //row_size=len('pk')+len('value1')+8 Byte+900 Byte=916 Byte
        //row_primarykey_size=len('pk')+8 Byte=10 Byte
        {
            primary_keys:{'pk':1},
            attributes:{'value1':String(900 Byte)}
        }
                                    
        ```

    -   Example 4

        Use DeleteRow to delete a row that does not exist.

        The size of the row is zero before and after the deletion operation. Regardless of whether the delete operation succeeds, at least one write CU is consumed. In this case, the DeleteRow operation consumes one write CU.

        The number of write and read CUs consumed by the DeleteRow operation:

        -   If you set condition to EXPECT\_EXIST, the number of consumed write and read CUs is calculated based on the formulas: Number of consumed write CUs = \(10 bytes/4 KB\) rounded up and Number of consumed read CUs = \(10 bytes/4 KB\) rounded up. \(The size of the primary key columns in the row is 10 bytes.\) In this case, the DeleteRow operation consumes one write CU and one read CU.
        -   If you set condition to IGNORE, the number of consumed write CUs is calculated based on the formula: Number of consumed write CUs = \(10 bytes/4 KB\) rounded up, and the number of consumed read CUs is 0. \(The size of the primary key columns in the row is 10 bytes.\) In this case, the DeleteRow operation consumes one write CU and zero read CUs.
        ```
        // The specified row does not exist.
        //row_size=0
        
        // The DeleteRow operation
        //row_size=0
        //row_primarykey_size=len('pk')+8 Byte=10 Byte
        {
            primary_keys:{'pk':1},
        }
                                    
        ```

-   Single-row read operations

    GetRow is the only single-row read operation. You must specify the entire primary key and the names of columns to return when you use GetRow.

    Single-row read operations do not consumed write CUs. The number of consumed read CUs is calculated based on the formula: Number of consumed read CUs = \[\(Size of primary key columns in the row + Size of attribute columns that are read\)/4 KB\] rounded up. If the specified row does not exist, one read CU is consumed by the operation. For more information, see [GetRow](/intl.en-US/API Reference/Operations/GetRow.md).

    Parameter

    The following table describes the parameters that can be configured for a single-row read operation.

    |Parameter|Description|
    |---------|-----------|
    |primary\_keys|The names and values of primary key columns.|
    |attributes|The names and values of attribute columns.|
    |columns\_to\_get|The names of the columns to return. You can specify the names of the primary key columns and the names of attribute columns. If you do not specify this parameter, all data in the row is returned. |

    Examples

    Use GetRow to read data from a row.

    The number of consumed read CUs is calculated based on the formula: Number of consumed read CUs = \(1216 bytes/4 KB\) rounded up. In this case, the GetRow operation consumes one read CU.

    ```
    // The row to read
    
    //row_size=len('pk')+len('value1')+len('value2')+8 Byte+1200 Byte+3100 Byte=4322 Byte
    {
        primary_keys:{'pk':1},
        attributes:{'value1':String(1200 Byte), 'value2':String(3100 Byte)}
    }
    
    // The GetRow operation
    // The size of obtained data: len ('pk') + len ('value1') + 8 bytes + 1200 bytes= 1216 bytes
    {
        primary_keys:{'pk':1},
        columns_to_get:{'value1'}
    }
                        
    ```


## Multi-row operations

-   Multi-row write operations

    BatchWriteRow is the only multi-row write operation. BatchWriteRow consists of multiple PutRow, UpdateRow, and DeleteRow operations.

    -   Each individual operation of BatchWriteRow is independently performed. The result for each individual operation is independently returned.

        The responses may contain successful results and failed results. Even if no errors are returned for the request, Tablestore examines each individual response to obtain the correct operation status.

    -   The number of write and read CUs consumed by each individual operation in BatchWriteRow is independently calculated. For more information, see [BatchWriteRow](/intl.en-US/API Reference/Operations/BatchWriteRow.md).
-   Multi-row read operations

    BatchGetRow is the only multi-row read operation. BatchGetRow consists of multiple GetRow operations.

    -   Each individual operation of BatchGetRow is independently performed. The result for each individual operation is independently returned.

        The responses may contain successful results and failed results. Even if no errors are returned for the request, Tablestore examines each individual response to obtain the correct operation status.

    -   The number of write and read CUs consumed by each individual operation in BatchGetRow is independently calculated. For more information, see [BatchGetRow](/intl.en-US/API Reference/Operations/BatchGetRow.md).
-   Range read operations

    GetRange is the only range read operation. You can use GetRange to return data within the specified range based on the primary key values.

    -   The number of read CUs consumed by a GetRange operation is calculated from the start point of the range to the start point of the next row that are unread. The number of consumed read CUs is calculated based on the formula: Number of consumed read CUs = \[\(Size of primary key columns in the rows + Size of attribute columns that are read\)/4 KB\] rounded up. For more information, see [GetRange](/intl.en-US/API Reference/Operations/GetRange.md).

        Example: The specified range to read contains 10 rows, and the total size of primary key columns and the attribute columns that are read is 330 bytes. The number of consumed read CUs is calculated based on the formula: Number of consumed read CUs = \(330 bytes/4 KB\) rounded up. In this case, the GetRange operation consumes one read CU.

    -   The GetRange operation may stop and return data in the following situations:
        -   The amount of data read reaches 4 MB.
        -   The number of rows read reaches 5,000.
        -   The number of rows returned reaches the limit.
        -   All reserved read throughput is consumed. You have no reserved read throughput to read the next row of data.
    Parameters

    The following table describes the parameters that can be configured for a GetRange operation.

    |Parameter|Description|
    |---------|-----------|
    |table\_name|The name of the table.|
    |direction|The direction to read data. This can be either forward or backward. Example: A table contains two primary keys A and B where the value of A is smaller than that of B. If you set direction to forward, the rows whose primary key values are equal to or larger than the value of A and smaller than the value of B are returned in order is from A to B. If you set direction to backward, the rows whose primary key values are equal to or larger than the value of B and smaller than the value of A are returned in order is from B to A. |
    |inclusive\_start\_primary\_key|The primary keys that are used to specify a range of data to read. Rows in Tablestore tables are sorted in ascending order of primary keys. GetRange specifies a left-closed and right-open range. The start and end points of ranges are composed of either valid primary keys or the virtual points INF\_MIN and INF\_MAX. The number of columns for the virtual point must be the same as that of the valid primary key. INF\_MIN indicates an infinitely small value of which all other values of other types are larger. INF\_MAX indicates an infinitely large value of which all other values are smaller. |
    |exclusive\_end\_primary\_key|
    |columns\_to\_get|The names of the columns to return. You can specify multiple columns to return in a request. If you do not specify columns\_to\_get, the entire row is returned. If a row is within the specified range to read based on the primary key value but does not contain the specified columns to return, the response does not include the row. |
    |limit|The maximum number of rows to return. An operation stops when the maximum number of rows are returned in a forward or backward direction, even if a part of rows within the specified range are not returned. |
    |cosumed\_read\_capacity\_unit|The read CUs that are consumed.|
    |primary\_keys|The names and values of primary key columns.|
    |attributes|The names and values of attribute columns.|
    |next\_start\_primary\_key|The start primary key of the next read. The value of next\_start\_primary\_key can be used to determine whether all data is read.     -   If the value of next\_start\_primary\_key is not empty in the response, the next\_start\_primary\_key value can be used as the start primary key for the next GetRange operation.
    -   If the value of next\_start\_primary\_key is empty in the response, all data within the range is returned. |

    Examples

    The following examples describe how to use the GetRange operation.

    The following table provides an example of a Tablestore table, which contains a STRING primary key column PK1, an INTEGER primary key column PK2, and two attributes columns Attr1 and Attr2.

    |PK1|PK2|Attr1|Attr2|
    |:--|:--|:----|:----|
    |'A'|2|'Hell'|'Bell'|
    |'A'|5|'Hello'|N/A|
    |'A'|6|N/A|'Blood'|
    |'B'|10|'Apple'|N/A|
    |'C'|1|N/A|N/A|
    |'C'|9|'Alpha'|N/A|

    -   Example 1

        Read data within a specified range.

        ```
        // Request
        table_name: "table_name"
        direction: FORWARD
        inclusive_start_primary_key: ("PK1", STRING, "A"), ("PK2", INTEGER, 2)
        exclusive_end_primary_key: ("PK1", STRING, "C"), ("PK2", INTEGER, 1)
        
        // Response
        cosumed_read_capacity_unit: 1
        rows: {
              {
                primary_key_columns:("PK1", STRING, "A"), ("PK2", INTEGER, 2)
                attribute_columns:("Attr1", STRING, "Hell"), ("Attr2", STRING, "Bell")
              },
              {
                primary_key_columns:("PK1", STRING, "A"), ("PK2", INTEGER, 5)
                attribute_columns:("Attr1", STRING, "Hello")
              },
              {
                primary_key_columns:("PK1", STRING, "A"), ("PK2", INTEGER, 6)
                attribute_columns:("Attr2", STRING, "Blood")
              },
              {
                primary_key_columns:("PK1", STRING, "B"), ("PK2", INTEGER, 10)
                attribute_columns:("Attr1", STRING, "Apple")
              }
            }
                                    
        ```

    -   Example 2

        Use INF\_MIN and INF\_MAX to read all data in the table.

        ```
        // Request
        table_name: "table_name"
        direction: FORWARD
        inclusive_start_primary_key: ("PK1", INF_MIN)
        exclusive_end_primary_key: ("PK1", INF_MAX)
        
        // Response
        cosumed_read_capacity_unit: 1
        rows: {
              {
                primary_key_columns:("PK1", STRING, "A"), ("PK2", INTEGER, 2)
                attribute_columns:("Attr1", STRING, "Hell"), ("Attr2", STRING, "Bell")
              },
              {
                primary_key_columns:("PK1", STRING, "A"), ("PK2", INTEGER, 5)
                attribute_columns:("Attr1", STRING, "Hello")
              },
              {
                primary_key_columns:("PK1", STRING, "A"), ("PK2", INTEGER, 6)
                attribute_columns:("Attr2", STRING, "Blood")
              },
              {
                primary_key_columns:("PK1", STRING, "B"), ("PK2", INTEGER, 10)
                attribute_columns:("Attr1", STRING, "Apple")
              }
              {
                primary_key_columns:("PK1", STRING, "C"), ("PK2", INTEGER, 1)
              }
              {
                primary_key_columns:("PK1", STRING, "C"), ("PK2", INTEGER, 9)
                attribute_columns:("Attr1", STRING, "Alpha")
              }
            }
                                    
        ```

    -   Example 3

        Use INF\_MIN and INF\_MAX as the minimum and maximum values for a part of primary key columns.

        ```
        // Request
        table_name: "table_name"
        direction: FORWARD
        inclusive_start_primary_key: ("PK1", STRING, "A"), ("PK2", INF_MIN)
        exclusive_end_primary_key: ("PK1", STRING, "A"), ("PK2", INF_MAX)
        
        // Response
        cosumed_read_capacity_unit: 1
        rows: {
              {
                primary_key_columns:("PK1", STRING, "A"), ("PK2", INTEGER, 2)
                attribute_columns:("Attr1", STRING, "Hell"), ("Attr2", STRING, "Bell")
              },
              {
                primary_key_columns:("PK1", STRING, "A"), ("PK2", INTEGER, 5)
                attribute_columns:("Attr1", STRING, "Hello")
              },
              {
                primary_key_columns:("PK1", STRING, "A"), ("PK2", INTEGER, 6)
                attribute_columns:("Attr2", STRING, "Blood")
              }
            }
                                    
        ```

    -   Example 4

        Read data within a specified range in a backward order.

        ```
        // Request
        table_name: "table_name"
        direction: BACKWARD
        inclusive_start_primary_key: ("PK1", STRING, "C"), ("PK2", INTEGER, 1)
        exclusive_end_primary_key: ("PK1", STRING, "A"), ("PK2", INTEGER, 5)
        
        // Response
        cosumed_read_capacity_unit: 1
        rows: {
              {
                primary_key_columns:("PK1", STRING, "C"), ("PK2", INTEGER, 1)
              },
              {
                primary_key_columns:("PK1", STRING, "B"), ("PK2", INTEGER, 10)
                attribute_columns:("Attr1", STRING, "Apple")
              },
              {
                primary_key_columns:("PK1", STRING, "A"), ("PK2", INTEGER, 6)
                attribute_columns:("Attr2", STRING, "Blood")
              }
            }
                                    
        ```

    -   Example 5

        Read data within a specified range if the specified columns do not contain primary key columns.

        ```
        // Request
        table_name: "table_name"
        direction: FORWARD
        inclusive_start_primary_key: ("PK1", STRING, "C"), ("PK2", INF_MIN)
        exclusive_end_primary_key: ("PK1", STRING, "C"), ("PK2", INF_MAX)
        columns_to_get: "Attr1"
        
        // Response
        cosumed_read_capacity_unit: 1
        rows: {
              {
                attribute_columns: {"Attr1", STRING, "Alpha"}
              }
            }
                                    
        ```

    -   Example 6

        Read data within a specified range if the specified columns do not contain primary key columns.

        ```
        // Request
        table_name: "table_name"
        direction: FORWARD
        inclusive_start_primary_key: ("PK1", STRING, "C"), ("PK2", INF_MIN)
        exclusive_end_primary_key: ("PK1", STRING, "C"), ("PK2", INF_MAX)
        columns_to_get: "Attr1", "PK1"
        
        // Response
        cosumed_read_capacity_unit: 1
        rows: {
              {
                primary_key_columns:("PK1", STRING, "C")
              }
              {
                primary_key_columns:("PK1", STRING, "C")
                attribute_columns:("Attr1", STRING, "Alpha")
              }
            }
                                    
        ```

    -   Example 7

        Use limit and next\_start\_primary\_key to read data within a specified range.

        ```
        // Request 1
        table_name: "table_name"
        direction: FORWARD
        inclusive_start_primary_key: ("PK1", STRING, "A"), ("PK2", INF_MIN)
        exclusive_end_primary_key: ("PK1", STRING, "A"), ("PK2", INF_MAX)
        limit: 2
        
        // Response 1
        cosumed_read_capacity_unit: 1
        rows: {
              {
                primary_key_columns:("PK1", STRING, "A"), ("PK2", INTEGER, 2)
                attribute_columns:("Attr1", STRING, "Hell"), ("Attr2", STRING, "Bell")
              },
              {
                primary_key_columns:("PK1", STRING, "A"), ("PK2", INTEGER, 5)
                attribute_columns:("Attr1", STRING, "Hello")
              }
            }
        next_start_primary_key:("PK1", STRING, "A"), ("PK2", INTEGER, 6)
        
        // Request 2
        table_name: "table_name"
        direction: FORWARD
        inclusive_start_primary_key: ("PK1", STRING, "A"), ("PK2", INTEGER, 6)
        exclusive_end_primary_key: ("PK1", STRING, "A"), ("PK2", INF_MAX)
        limit: 2
        
        // Response 2
        cosumed_read_capacity_unit: 1
        rows: {
              {
                primary_key_columns:("PK1", STRING, "A"), ("PK2", INTEGER, 6)
                attribute_columns:("Attr2", STRING, "Blood")
              }
            }
                                    
        ```

    -   Example 8

        Calculate the number of read CUs consumed by a GetRange operation. The number of consumed read CUs in the GetRange request is calculated based on the formula: Number of consumed read CUs = \[\(11 bytes+ 24 bytes+ 1016 bytes\)/4 KB\] rounded up. In this case, the GetRange operation consumes one read CU. The following formulas describe how to calculate the size of data that is read in each row:

        -   The size of data read from the first row: len \('PK1'\) + 8 bytes = 11 bytes
        -   The size of data read from the second row: len \('PK1'\) + 8 bytes + len \('Attr1'\) + 8 bytes = 24 bytes
        -   The size of data read from the third row: len \('PK1'\) + 8 bytes + len \('Attr1'\) + 1000 bytes = 1016 bytes
        Perform the GetRange operation on the following Table. PK1 is a primary key column of the table. Attr1 and Attr2 are attribute columns of the table.

        |PK1|Attr1|Attr2|
        |:--|:----|:----|
        |1|N/A|A string \(1000 bytes\)|
        |2|8|A string \(1000 bytes\)|
        |3|A string \(1000 bytes\)|N/A|
        |4|A string \(1000 bytes\)|A string \(1000 bytes\)|

        ```
        // Request
        table_name: "table2_name"
        direction: FORWARD
        inclusive_start_primary_key: ("PK1", INTEGER, 1)
        exclusive_end_primary_key: ("PK1", INTEGER, 4)
        columns_to_get: "PK1", "Attr1"
        
        // Response
        cosumed_read_capacity_unit: 1
        rows: {
              {
                primary_key_columns:("PK1", INTEGER, 1)
              },
              {
                primary_key_columns:("PK1", INTEGER, 2),
                attribute_columns:("Attr1", INTEGER, 8)
              },
              {
                primary_key_columns:("PK1", INTEGER, 3),
                attribute_columns:("Attr1", STRING, A string (1000 bytes))
              },
            }
                                    
        ```


## Usage

You can use the following Tablestore SDKs to perform basic operations on data:

-   Tablestore SDK for Java: [Single-row operations](/intl.en-US/SDK Reference/Java SDK/Single-row operations.md) and [Multi-row operations](/intl.en-US/SDK Reference/Java SDK/Multiple-row operations.md)
-   Tablestore SDK for Go: [Single-row operations](/intl.en-US/SDK Reference/Go SDK/Single-row operations.md) and [Multi-row operations](/intl.en-US/SDK Reference/Go SDK/Multi-row operations.md)
-   Tablestore SDK for Python: [Single-row operations](/intl.en-US/SDK Reference/Python SDK/Single-row operations.md) and [Multi-row operations](/intl.en-US/SDK Reference/Python SDK/Multi-row operations.md)
-   Tablestore SDK for Node.js: [Single-row operations](/intl.en-US/SDK Reference/NodeJS SDK/Single-row operations.md) and [Multi-row operations](/intl.en-US/SDK Reference/NodeJS SDK/Multiple-row operations.md)
-   Tablestore SDK for .NET: [Single-row operations](/intl.en-US/SDK Reference/. .NET SDK/Single-row operations.md) and [Multi-row operations](/intl.en-US/SDK Reference/. .NET SDK/Multiple-row operations.md)
-   Tablestore SDK for PHP: [Single-row operations](/intl.en-US/SDK Reference/PHP SDK/Single-row operations.md) and [Multi-row operations](/intl.en-US/SDK Reference/PHP SDK/Multi-row operations.md)

## Best practices

For more information, see [Data Operations](/intl.en-US/Best Practices/Data Operations.md).

## Billing methods

For more information, see [Billing overview](/intl.en-US/Pricing/Billing overview.md).

## Error codes

For more information about error codes, see [t20552.md\#](/intl.en-US/API Reference/Error messages.md).

