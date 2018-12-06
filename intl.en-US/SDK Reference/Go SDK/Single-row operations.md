# Single-row operations {#reference4373 .reference}

The Table Store SDK provides the following single-row operation APIs: PutRow, GetRow, UpdateRow, and DeleteRow.

## PutRow {#section_at2_rs2_2fb .section}

Inserts data into the specified row.

**API**

```language-go
	// @param PutRowRequest    Encapsulate the parameters required to perform the PutRow operation
	// @return PutRowResponse
	PutRow(request *PutRowRequest) (*PutRowResponse, error)

```

**Example**

```language-go
	putRowRequest := new(tablestore.PutRowRequest)
	putRowChange := new(tablestore.PutRowChange)
	putRowChange.TableName = tableName
	putPk := new(tablestore.PrimaryKey)
	putPk.AddPrimaryKeyColumn("pk1", "pk1value1")
	putPk.AddPrimaryKeyColumn("pk2", int64(2))
	putPk.AddPrimaryKeyColumn("pk3", []byte("pk3"))

	putRowChange.PrimaryKey = putPk
	putRowChange.AddColumn("col1", "col1data1")
	putRowChange.AddColumn("col2", int64(3))
	putRowChange.AddColumn("col3", []byte("test"))
	putRowChange.SetCondition(tablestore.RowExistenceExpectation_IGNORE)
	putRowRequest.PutRowChange = putRowChange
	_, err := client.PutRow(putRowRequest)

	if err ! = nil {
		fmt.Println("putrow failed with error:", err)
	} else {
		fmt.Println("putrow finished")
	}

```

-   RowExistenceExpectation.IGNORE indicates that new data is still inserted no matter whether the specified row exists or not. If the inserted data is the same as the existing data, the existing data is overwritten.
-   RowExistenceExpectation.EXPECT\_EXIST indicates that new data is inserted only when the specified row exists. The existing data is overwritten.
-   RowExistenceExpectation.EXPECT\_NOT\_EXIST indicates that data is inserted only when the specified row does not exist.

**Note:** Obtain the full sample codes at [PutRow@GitHub](https://github.com/aliyun/aliyun-tablestore-go-sdk/blob/master/sample/SingleRowOperation.go).

## GetRow { .section}

This API reads a single data row based on a given primary key.

**API**

```language-go
	//Return a row of data from a table
	//
	// @param GetRowRequest             Encapsulate the parameters required to perform the GetRow operation
	// @return  GetRowResponse          Content of the response to the GetRow operation
	GetRow(request *GetRowRequest) (*GetRowResponse, error)

```

**Example**

Read a data row.

```language-go
getRowRequest := new(tablestore.GetRowRequest)
	criteria := new(tablestore.SingleRowQueryCriteria);
	putPk := new(tablestore.PrimaryKey)
	putPk.AddPrimaryKeyColumn("pk1", "pk1value1")
	putPk.AddPrimaryKeyColumn("pk2", int64(2))
	putPk.AddPrimaryKeyColumn("pk3", []byte("pk3"))

	criteria.PrimaryKey = putPk
	getRowRequest.SingleRowQueryCriteria = criteria
	getRowRequest.SingleRowQueryCriteria.TableName = tableName
	getRowRequest.SingleRowQueryCriteria.MaxVersion = 1
	getResp, err := client.GetRow(getRowRequest)
	if err ! = nil {
		fmt.Println("getrow failed with error:", err)
	} else {
		fmt.Println("get row col0 result is ",getResp.Columns[0]. ColumnName, getResp.Columns[0]. Value,)
	}

```

**Note:** Obtain the full sample codes at [GetRow@GitHub](https://github.com/aliyun/aliyun-tablestore-go-sdk/blob/master/sample/SingleRowOperation.go).

## UpdateRow { .section}

Updates the data of the specified row. If the row does not exist, a new row is added. If the row exists, the values of the specified columns are added, modified, or deleted based on the request content.

**API**

```language-go
	// Update a data row in a table
	// @param UpdateRowRequest      Encapsulate the parameters required to perform the UpdateRow operation
	// @return UpdateRowResponse    Content of the response to the UpdateRow operation
	UpdateRow(request *UpdateRowRequest) (*UpdateRowResponse, error)

```

**Example**

Update a data row.

```language-go
	updateRowRequest := new(tablestore.UpdateRowRequest)
	updateRowChange := new(tablestore.UpdateRowChange)
	updateRowChange.TableName = tableName
	updatePk := new(tablestore.PrimaryKey)
	updatePk.AddPrimaryKeyColumn("pk1", "pk1value1")
	updatePk.AddPrimaryKeyColumn("pk2", int64(2))
	updatePk.AddPrimaryKeyColumn("pk3", []byte("pk3"))
	updateRowChange.PrimaryKey = updatePk
	updateRowChange.DeleteColumn("col1")
	updateRowChange.PutColumn("col2", int64(77))
	updateRowChange.PutColumn("col4", "newcol3")
	updateRowChange.SetCondition(tablestore.RowExistenceExpectation_EXPECT_EXIST)
	updateRowRequest.UpdateRowChange = updateRowChange
	_, err := client.UpdateRow(updateRowRequest)

	if err ! = nil {
		fmt.Println("update failed with error:", err)
	} else {
		fmt.Println("update row finished")
	}

```

**Note:** Obtain the full sample codes at [UpdateRow@GitHub](https://github.com/aliyun/aliyun-tablestore-go-sdk/blob/master/sample/SingleRowOperation.go).

## DeleteRow { .section}

**API**

```language-go
	// Delete a data row from a table
	// @param DeleteRowRequest           Encapsulate the parameters required to perform the DeleteRow operation
	// @return DeleteRowResponse         Content of the response to the DeleteRow operation
	DeleteRow(request *DeleteRowRequest) (*DeleteRowResponse, error)

```

**Example**

This API deletes a data row.

```language-go
	deleteRowReq := new(tablestore.DeleteRowRequest)
	deleteRowReq.DeleteRowChange = new(tablestore.DeleteRowChange)
	deleteRowReq.DeleteRowChange.TableName = tableName
	deletePk := new(tablestore.PrimaryKey)
	deletePk.AddPrimaryKeyColumn("pk1", "pk1value1")
	deletePk.AddPrimaryKeyColumn("pk2", int64(2))
	deletePk.AddPrimaryKeyColumn("pk3", []byte("pk3"))
	deleteRowReq.DeleteRowChange.PrimaryKey = deletePk
	deleteRowReq.DeleteRowChange.SetCondition(tablestore.RowExistenceExpectation_EXPECT_EXIST)
	clCondition1 := tablestore.NewSingleColumnCondition("col2", tablestore.CT_EQUAL, int64(3))
	deleteRowReq.DeleteRowChange.SetColumnCondition(clCondition1)
	_, err := client.DeleteRow(deleteRowReq)
	if err ! = nil {
		fmt.Println("delete failed with error:", err)
	} else {
		fmt.Println("delete row finished")
	}

```

**Note:** Obtain the full sample codes at [DeleteRow@GitHub](https://github.com/aliyun/aliyun-tablestore-go-sdk/blob/master/sample/SingleRowOperation.go).

