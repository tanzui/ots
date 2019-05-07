# Query the description information of a table {#concept_xpz_xm5_hgb .concept}

You can call the DescribeTable operation to query the structure information and the reserved read/write throughput of a table.

**API**

```language-go
// Query the descriptive information of a table by table name.
DescribeTable(request *DescribeTableRequest) (*DescribeTableResponse, error)

```

**Examples**

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

**Note:** Code details can be obtained at [describeTable@GitHub](https://github.com/aliyun/aliyun-tablestore-go-sdk/blob/master/sample/TableOperation.go).

