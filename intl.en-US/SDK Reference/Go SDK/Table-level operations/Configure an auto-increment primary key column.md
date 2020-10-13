# Configure an auto-increment primary key column

## Prerequisites

The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Go SDK/Initialization.md).

## Usage notes

1.  When you create a table, you cannot set the partition key to an auto-increment primary key column.

    An auto-increment primary key column can only be an integer column. The generated value for an auto-increment primary key column is a 64-bit signed long integer.

2.  When you write data to a table, you do not need to specify a specific value for the auto-increment column. Instead, you need only to set the value of the auto-increment column to the auto-increment primary key column.

    If you want to obtain the value of the auto-increment column after data is written to the table, you can set ReturnType to RT\_PK.

    When you query data, you must specify the values of all primary key columns. To obtain a complete primary key value, you can set ReturnType to RT\_PK in PutRow, UpdateRow, or BatchWriteRow.


## Examples

When you use auto-increment primary key columns, you can call the CreateTable, PutRow, UpdateRow, or BatchWriteRow operation.

1.  Create a table

    To create an auto-increment primary key column when you create a table, you must set the attribute of the primary key column to AUTO\_INCREMENT.

    ```
    import (
        "fmt"
        "github.com/aliyun/aliyun-tablestore-go-sdk/tablestore"
    )
    
    func CreateTableKeyAutoIncrementSample(client *tablestore.TableStoreClient) {
        createtableRequest := new(tablestore.CreateTableRequest)
        // Create a table that contains three primary key columns: pk1 of the STRING type, pk2 of the INTEGER type, and pk3 of the BINARY type. pk2 is the auto-increment primary key column.
        tableMeta := new(tablestore.TableMeta)
        tableMeta.TableName = "incrementsampletable"
        tableMeta.AddPrimaryKeyColumn("pk1", tablestore.PrimaryKeyType_STRING)
        tableMeta.AddPrimaryKeyColumnOption("pk2", tablestore.PrimaryKeyType_INTEGER, tablestore.AUTO_INCREMENT)
        tableMeta.AddPrimaryKeyColumn("pk3", tablestore.PrimaryKeyType_BINARY)
        tableOption := new(tablestore.TableOption)
        tableOption.TimeToAlive = -1
        tableOption.MaxVersion = 3
        reservedThroughput := new(tablestore.ReservedThroughput)
        reservedThroughput.Readcap = 0
        reservedThroughput.Writecap = 0
        createtableRequest.TableMeta = tableMeta
        createtableRequest.TableOption = tableOption
        createtableRequest.ReservedThroughput = reservedThroughput
    
        client.CreateTable(createtableRequest)
    }
    ```

2.  Write data

    When you write data to a table, you do not need to specify a specific value for the auto-increment column. Instead, you need only to set the value of the auto-increment column to the auto-increment primary key column.

    ```
    import (
        "fmt"
        "github.com/aliyun/aliyun-tablestore-go-sdk/tablestore"
    )
    
    func PutRowWithKeyAutoIncrementSample(client *tablestore.TableStoreClient) {
        fmt.Println("begin to put row")
        putRowRequest := new(tablestore.PutRowRequest)
        putRowChange := new(tablestore.PutRowChange)
        putRowChange.TableName = "incrementsampletable"
        // Specify primary key columns, which must be added in the order specified when the table is created. Set pk2 to the auto-increment primary key column.
        putPk := new(tablestore.PrimaryKey)
        putPk.AddPrimaryKeyColumn("pk1", "pk1value1")
        putPk.AddPrimaryKeyColumnWithAutoIncrement("pk2")
        putPk.AddPrimaryKeyColumn("pk3", []byte("pk3"))
        putRowChange.PrimaryKey = putPk
        putRowChange.AddColumn("col1", "col1data1")
        putRowChange.AddColumn("col2", int64(100))
        putRowChange.SetCondition(tablestore.RowExistenceExpectation_IGNORE)
        putRowRequest.PutRowChange = putRowChange
        _, err := client.PutRow(putRowRequest)
    
        if err != nil {
            fmt.Println("put row failed with error:", err)
        } else {
            fmt.Println("put row finished")
        }
    }
    ```


