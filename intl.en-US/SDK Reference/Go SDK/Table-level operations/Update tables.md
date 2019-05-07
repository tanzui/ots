# Update tables {#concept_syv_wm5_hgb .concept}

You can call the UpdateTable operation to update the reserved read/write throughput of a table.

**API**

```language-go
// Update the values of the TableOptions and ReservedThroughput fields of a table.
UpdateTable(request *UpdateTableRequest) (*UpdateTableResponse, error)

```

**Examples**

The following code provides an example of how to update the configurations of a table such as setting MaxVersion to 5:

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

**Note:** Code details can be obtained at [updateTable@GitHub](https://github.com/aliyun/aliyun-tablestore-go-sdk/blob/master/sample/TableOperation.go).

