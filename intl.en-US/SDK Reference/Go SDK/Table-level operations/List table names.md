# List table names {#concept_zrt_vf5_hgb .concept}

You can call the ListTable operation to obtain the names of all tables that are created in the current instance.

**API**

```language-Go
// List the names of all tables in the current instance. If the operation succeeds, names of all tables in the current instance are returned.
ListTable() (*ListTableResponse, error)

```

**Examples**

The following code provides an example of how to obtain the names of all tables in the current instance:

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

**Note:** Code details can be obtained at [listTable@GitHub](https://github.com/aliyun/aliyun-tablestore-go-sdk/blob/master/sample/TableOperation.go).

