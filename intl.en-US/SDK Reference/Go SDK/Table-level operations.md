# Table-level operations {#reference3590 .reference}

The Table Store SDK provides the following APIs for operating tables: CreateTable, ListTable, DeleteTable, UpdateTable, and DescribeTable.

## CreateTable { .section}

Creates a table based on the given table structure information.

When creating a table in Table Store, you must specify the table’s primary keys. A primary key contains one to four primary key columns. Each primary key column has a name and a type.

**API**

```language-go
    // Description: This operation creates a table based on the entered table information
    // request is the instance of the CreateTableRequest class, which contains tableMeta, TableOption, and ReservedThroughput
   // For more information, see the documentation for the TableMeta class. After a table is created, it generally takes one minute to load the partitions
   // After that, you can perform table operations
   // Return: CreateTableResponse
		CreateTable(request *CreateTableRequest) (*CreateTableResponse, error)

```

**Note:** After a table is created, it takes several seconds to load the table. Do not perform any operations during this time.

**Example**

Create a table with two primary key columns and a reserved read/write throughput of \(0,0\).

```language-go
        //Create a schema for the primary key columns, including the quantity, names, and types of primary keys
        //The first primary key column, the sharding column, is of integer type and named pk0
        //The second primary key column is of integer type and named pk1

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

**Note:** Obtain the full sample codes at [createTable@GitHub](https://github.com/aliyun/aliyun-tablestore-go-sdk/blob/master/sample/TableOperation.go).

## ListTable { .section}

Obtains the names of all tables under the current instance.

**API**

```language-Go
	// List all tables. If the operation succeeds, names of all tables are returned
	ListTable() (*ListTableResponse, error)

```

**Example**

Obtain the names of all tables under an instance.

```language-Go
	tables, err := client.ListTable()
	if err ! = nil {
		fmt.Println("Failed to list table")
	} else {
		fmt.Println("List table result is")
		for _, table := range (tables.TableNames) {
			fmt.Println("TableName: ", table)
		}
	}

```

**Note:** Obtain the full sample codes at [listTable@GitHub](https://github.com/aliyun/aliyun-tablestore-go-sdk/blob/master/sample/TableOperation.go).

## UpdateTable { .section}

Updates the reserved read or write throughput value of the specified table.

**API**

```language-go
	// Update TableOptions and ReservedThroughput of a table
	UpdateTable(request *UpdateTableRequest) (*UpdateTableResponse, error)

```

**Example**

Set maxVersions of a table to 5.

```language-go
	updateTableReq := new(tablestore.UpdateTableRequest)
	updateTableReq.TableName = tableName
	updateTableReq.TableOption = new(tablestore.TableOption)
	updateTableReq.TableOption.TimeToAlive = -1
	updateTableReq.TableOption.MaxVersion = 5

	_, err := client.UpdateTable(updateTableReq)

	if (err ! = nil) {
		fmt.Println("failed to update table with error:", err)
	} else {
		fmt.Println("update finished")
	}

```

**Note:** Obtain the full sample codes at [updateTable@GitHub](https://github.com/aliyun/aliyun-tablestore-go-sdk/blob/master/sample/TableOperation.go).

## DescribeTable { .section}

Queries the structure information and the reserved read/write throughput value of the specified table.

**API**

```language-go
// Query the description of a table using the table name
DescribeTable(request *DescribeTableRequest) (*DescribeTableResponse, error)

```

**Example**

```language-go
	describeTableReq := new(tablestore.DescribeTableRequest)
	describeTableReq.TableName = tableName
	describ, err := client.DescribeTable(describeTableReq)
	if err ! = nil {
		fmt.Println("failed to update table with error:", err)
	} else {
		fmt.Println("DescribeTableSample finished. Table meta:", describ.TableOption.MaxVersion, describ.TableOption.TimeToAlive)
	}


```

**Note:** Obtain the full sample codes at [describeTable@GitHub](https://github.com/aliyun/aliyun-tablestore-go-sdk/blob/master/sample/TableOperation.go).

## DeleteTable { .section}

Deletes the specified table under an instance.

**API**

DeleteTable\(request DeleteTableRequest\) \(DeleteTableResponse, error\)

**Example**

Delete a table.

```language-Go
	deleteReq := new(tablestore.DeleteTableRequest)
	deleteReq.TableName = tableName
	_, err := client.DeleteTable(deleteReq)
	if (err ! = nil) {
		fmt.Println("Failed to delete table with error:", err)
	} else {
		fmt.Println("Delete table finished")
	}

```

**Note:** Obtain the full sample codes at [deleteTable@GitHub](https://github.com/aliyun/aliyun-tablestore-go-sdk/blob/master/sample/TableOperation.go).

