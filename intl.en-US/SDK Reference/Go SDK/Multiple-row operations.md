# Multiple-row operations {#reference4482 .reference}

The Table Store SDK provides the following multi-row operation APIs: BatchGetRow, BatchWriteRow, GetRange, and GetByIterator.

## BatchGetRow { .section}

Reads several data rows in batches from one or more tables.

The BatchGetRow operation can be viewed as a set of multiple GetRow operations. Each operation is executed, results are returned, and capacity units are consumed independently.

Compared to the execution of a large number of GetRow operations, the BatchGetRow operation effectively reduces the request response time and increases the data read rate.

**API**

```language-go
	//Return multiple data rows in a table
	//
	// @param BatchGetRowRequest             Encapsulate the parameters required to perform the BatchGetRow operation
	// @return  BatchGetRowResponse          Content of the response to the BatchGetRow operation
	BatchGetRow(request *BatchGetRowRequest) (*BatchGetRowResponse, error)

```

**Example**

Read 10 data rows in batches.

```
batchGetReq := &tablestore.BatchGetRowRequest{}
mqCriteria := &tablestore.MultiRowQueryCriteria{}

for i := 0; i < 10; i++ {
	pkToGet := new(tablestore.PrimaryKey)
	pkToGet.AddPrimaryKeyColumn("pk1", "pk1value1")
	pkToGet.AddPrimaryKeyColumn("pk2", int64(i))
	pkToGet.AddPrimaryKeyColumn("pk3", []byte("pk3"))
	mqCriteria.AddRow(pkToGet)
	mqCriteria.MaxVersion = 1
}
mqCriteria.TableName = tableName
batchGetReq.MultiRowQueryCriteria = append(batchGetReq.MultiRowQueryCriteria, mqCriteria)
batchGetResponse, err := client.BatchGetRow(batchGetReq)

if err ! = nil {
	fmt.Println("batachget failed with error:", err)
} else {
	fmt.Println("batchget finished")
}

```

**Note:** 

-   BatchGetRow supports filtering using conditional statements.
-   Obtain the full sample codes at [BatchGetRow@GitHub](https://github.com/aliyun/aliyun-tablestore-go-sdk/blob/master/sample/MultipleRowOperation.go).

## BatchWriteRow { .section}

Inserts, modifies, or deletes several data rows in batches in one or more tables.

It is essentially a set of multiple PutRow, UpdateRow, and DeleteRow operations. Each operation is executed, results are returned independently, and capacity units are consumed independently.

**API**

```language-go
	// Add, delete, or modify multiple data rows in multiple tables
	//
	// @param BatchWriteRowRequest             Encapsulate the parameters required to perform the BatchWriteRow operation
	// @return  BatchWriteRowResponse          Content of the response to the BatchWriteRow operation
	BatchWriteRow(request *BatchWriteRowRequest) (*BatchWriteRowResponse,error)

```

**Example**

Write 100 data rows in batches.

```language-go
	batchWriteReq := &tablestore.BatchWriteRowRequest{}
	for i := 0; i < 100; i++ {
		putRowChange := new(tablestore.PutRowChange)
		putRowChange.TableName = tableName
		putPk := new(tablestore.PrimaryKey)
		putPk.AddPrimaryKeyColumn("pk1", "pk1value1")
		putPk.AddPrimaryKeyColumn("pk2", int64(i))
		putPk.AddPrimaryKeyColumn("pk3", []byte("pk3"))
		putRowChange.PrimaryKey = putPk
		putRowChange.AddColumn("col1", "fixvalue")
		putRowChange.SetCondition(tablestore.RowExistenceExpectation_IGNORE)
		batchWriteReq.AddRowChange(putRowChange)
	}

	response, err := client.BatchWriteRow(batchWriteReq)
	if err ! = nil {
		fmt.Println("batch request failed with:", response)
	} else {
		fmt.Println("batch write row finished")
	}

```

**Note:** 

-   BatchWriteRow supports filtering using conditional statements.
-   Obtain the full sample codes at [BatchWriteRow@GitHub](https://github.com/aliyun/aliyun-tablestore-go-sdk/blob/master/sample/MultipleRowOperation.go).

## GetRange { .section}

This API reads data within the specified primary key range.

**API**

```language-go
	// Read multiple data rows within the specified range in a table.
	//
	// @param GetRangeRequest            Encapsulate the parameters required to perform the GetRange operation
	// @return GetRangeResponse          Content of the response to the GetRange operation
	GetRange(request *GetRangeRequest) (*GetRangeResponse,error)

```

**Example**

Read data within the specified range.

```language-go
getRangeRequest := &tablestore.GetRangeRequest{}
	rangeRowQueryCriteria := &tablestore.RangeRowQueryCriteria{}
	rangeRowQueryCriteria.TableName = tableName

	startPK := new(tablestore.PrimaryKey)
	startPK.AddPrimaryKeyColumnWithMinValue("pk1")
	startPK.AddPrimaryKeyColumnWithMinValue("pk2")
	startPK.AddPrimaryKeyColumnWithMinValue("pk3")
	endPK := new(tablestore.PrimaryKey)
	endPK.AddPrimaryKeyColumnWithMaxValue("pk1")
	endPK.AddPrimaryKeyColumnWithMaxValue("pk2")
	endPK.AddPrimaryKeyColumnWithMaxValue("pk3")
	rangeRowQueryCriteria.StartPrimaryKey = startPK
	rangeRowQueryCriteria.EndPrimaryKey = endPK
	rangeRowQueryCriteria.Direction = tablestore.FORWARD
	rangeRowQueryCriteria.MaxVersion = 1
	rangeRowQueryCriteria.Limit = 10
	getRangeRequest.RangeRowQueryCriteria = rangeRowQueryCriteria

	getRangeResp, err := client.GetRange(getRangeRequest)

	fmt.Println("get range result is " ,getRangeResp)

	for ; ; {
		if err ! = nil {
			fmt.Println("get range failed with error:", err)
		}
		if (len(getRangeResp.Rows) > 0) {
			for _, row := range getRangeResp.Rows {
				fmt.Println("range get row with key", row.PrimaryKey.PrimaryKeys[0]. Value, row.PrimaryKey.PrimaryKeys[1]. Value, row.PrimaryKey.PrimaryKeys[2]. Value)
			}
			if getRangeResp.NextStartPrimaryKey == nil {
				break
			} else {
				fmt.Println("next pk is :", getRangeResp.NextStartPrimaryKey.PrimaryKeys[0]. Value, getRangeResp.NextStartPrimaryKey.PrimaryKeys[1]. Value, getRangeResp.NextStartPrimaryKey.PrimaryKeys[2]. Value)
				getRangeRequest.RangeRowQueryCriteria.StartPrimaryKey = getRangeResp.NextStartPrimaryKey
				getRangeResp, err = client.GetRange(getRangeRequest)
			}
		} else {
			break
		}

		fmt.Println("continue to query rows")
	}
	fmt.Println("putrow finished")

```

**Note:** 

-   GetRange supports filtering using conditional statements.
-   When performing the GetRange operation, note that the data may be paged.
-   Obtain the full sample codes at [GetRange@GitHub](https://github.com/aliyun/aliyun-tablestore-go-sdk/blob/master/sample/MultipleRowOperation.go).

