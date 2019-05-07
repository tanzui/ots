# Create tables {#concept_j1s_rf5_hgb .concept}

You can call the CreateTable operation to create a table based on specified table schema information.

When creating a table in Table Store, you must specify the primary key of the table. A primary key contains one to four primary key columns. Each primary key column has a name and a data type.

**API**

```language-go
  // Create the table based on specified table structure information.
  // request is the instance of the CreateTableRequest class, which contains tableMeta, TableOption, and ReservedThroughput
 // For more information, see the documentation for the TableMeta class. After a table is created, it takes one minute to load the partitions.
 // After that, you can perform other operations.
 // Response: CreateTableResponse
		CreateTable(request *CreateTableRequest) (*CreateTableResponse, error)

```

**Note:** After the table is created in Table Store, it takes several seconds to load the table. Do not perform any operations during this period.

**Examples**

The following code provides an example of how to create a table with two primary key columns and a reserved read/write throughput of \(0,0\):

```language-go
 //Create a schema for the primary key columns. The schema contains the quantities, names, and types of the primary key columns.
 //The first primary key column is the partition column. It is of the Integer type and named pk0.
 //The second primary key column is of the Integer type and named pk1.

 tableMeta := new(tablestore.TableMeta)
 tableMeta.TableName = tableName
 tableMeta.AddPrimaryKeyColumn("pk0", tablestore.PrimaryKeyType_INTEGER)
 tableMeta.AddPrimaryKeyColumn("pk1", tablestore.PrimaryKeyType_STRING)
 tableOption := new(tablestore.TableOption)
 tableOption.TimeToAlive = -1
 tableOption.MaxVersion = 3
 reservedThroughput := new(tablestore.ReservedThroughput)
 reservedThroughput.Readcap = 0
 reservedThroughput.Writecap = 0
 createtableRequest.TableMeta = tableMeta
 createtableRequest.TableOption = tableOption
 createtableRequest.ReservedThroughput = reservedThroughput
 response, err = client.CreateTable(createtableRequest)
 if (err ! = nil) {
     fmt.Println("Failed to create table with error:", err)
 } else {
    fmt.Println("Create table finished")
 }

```

**Note:** Code details can be obtained at [createTable@GitHub](https://github.com/aliyun/aliyun-tablestore-go-sdk/blob/master/sample/TableOperation.go).

