# Delete tables {#concept_ql1_zm5_hgb .concept}

You can call the DeleteTable operation to delete a table from a specified instance.

**API**

DeleteTable\(request \*DeleteTableRequest\) \(\*DeleteTableResponse, error\)

**Examples**

The following code provides an example of how to delete a table from a specified instance:

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

**Note:** Code details can be obtained at [deleteTable@GitHub](https://github.com/aliyun/aliyun-tablestore-go-sdk/blob/master/sample/TableOperation.go).

