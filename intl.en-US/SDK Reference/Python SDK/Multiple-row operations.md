# Multiple-row operations {#reference9948 .reference}

The Table Store SDK provides the following multi-row operation APIs: BatchGetRow, BatchWriteRow, and GetRange.

## BatchGetRow { .section}

Reads several data rows in batches from one or more tables.

The BatchGetRow operation is essentially a set of multiple GetRow operations. Each operation is executed, results are returned, and capacity units are consumed independently.

Compared to the execution of a large number of GetRow operations, the BatchGetRow operation effectively reduces the request response time and increases the data read rate.

**API**

```language-python
        """
        Description: This operation batch reads data from multiple rows.
        request = BatchGetRowRequest()
        request.add(TableInBatchGetRowItem(myTable0, primary_keys, column_to_get=None, column_filter=None))
        request.add(TableInBatchGetRowItem(myTable1, primary_keys, column_to_get=None, column_filter=None))
        request.add(TableInBatchGetRowItem(myTable2, primary_keys, column_to_get=None, column_filter=None))
        request.add(TableInBatchGetRowItem(myTable3, primary_keys, column_to_get=None, column_filter=None))
        response = client.batch_get_row(request)
        ``response`` indicates the returned results of the tablestore.metadata.BatchGetRowResponse type.
        """
		def batch_get_row(self, request): 

```

**Examples**

In this example, 3 rows of data is batch read.

```language-python
    # Columns to be returned
    columns_to_get = ['name', 'mobile', 'address', 'age']
	
	# Read three rows
    rows_to_get = [] 
    for i in range(0, 3):
        primary_key = [('gid',i), ('uid',i+1)]
        rows_to_get.append(primary_key)
		
	# Filter conditions: name is "Johb" and address is "China".
    cond = CompositeColumnCondition(LogicalOperator.AND)
    cond.add_sub_condition(SingleColumnCondition("name", "John", ComparatorType.EQUAL))
    cond.add_sub_condition(SingleColumnCondition("address", 'China', ComparatorType.EQUAL))
	
	# Construct a batch read request.
    request = BatchGetRowRequest()
	
	# Add table: Rows to be read from the table indicated by table_name. The last parameter 1 indicates reading the latest version.
    request.add(TableInBatchGetRowItem(table_name, rows_to_get, columns_to_get, cond, 1))
	
	# Add table: Rows to be read from the table indicated by notExistTable.
    request.add(TableInBatchGetRowItem('notExistTable', rows_to_get, columns_to_get, cond, 1))
	
	try:
   		result = client.batch_get_row(request) 
    	print ('Result status: %s'%(result.is_all_succeed()))

		table_result_0 = result.get_result_by_table(table_name)
    	table_result_1 = result.get_result_by_table('notExistTable')
    	print ('Check first table\'s result:')
    	for item in table_result_0: 
        	if item.is_ok:
            	print ('Read succeed, PrimaryKey: %s, Attributes: %s' % (item.row.primary_key, item.row.attribute_columns))
        	else:
            	print ('Read failed, error code: %s, error message: %s' % (item.error_code, item.error_message))
    	print ('Check second table\'s result:')
    	for item in table_result_1:
        	if item.is_ok:
            	print ('Read succeed, PrimaryKey: %s, Attributes: %s' % (item.row.primary_key, item.row.attribute_columns))
        	else: 
            	print ('Read failed, error code: %s, error message: %s' % (item.error_code, item.error_message))
	# The client is abnormal, which is typically due to incorrect parameters or a network error.
	except OTSClientError as e:
		print "get row failed, http_status:%d, error_message:%s" % (e.get_http_status(), e.get_error_message())
	# The server is abnormal, which is typically due to incorrect parameters or a network error.
	except OTSServiceError as e:
		print "get row failed, http_status:%d, error_code:%s, error_message:%s, request_id:%s" % (e.get_http_status(), e.get_error_code(), e.get_error_message(), e.get_request_id())

```

Obtain code details at [BatchGetRow@GitHub](https://github.com/aliyun/aliyun-tablestore-python-sdk/blob/master/examples/batch_get_row.py).

## BatchWriteRow { .section}

Inserts, modifies, or deletes several data rows in batches in one or more tables.

The BatchWriteRow operation is essentially a set of multiple PutRow, UpdateRow, and DeleteRow operations. Each operation is executed, results are returned, and capacity units are consumed independently.

**API**

```language-python
        """
        Description: This operation batch modifies data from multiple rows.
        request = MiltiTableInBatchWriteRowItem()
        request.add(TableInBatchWriteRowItem(table0, row_items))
        request.add(TableInBatchWriteRowItem(table1, row_items))
        response = client.batch_write_row(request)
        ``response`` indicates the returned results of the tablestore.metadata.BatchWriteRowResponse type. 
        """
		def batch_write_row(self, request):

```

**Examples**

In this example, data is batch written.

```language-python
    put_row_items = []
	## Add rows by PutRow.
    for i in range(0, 10):
        primary_key = [('gid',i), ('uid',i+1)]
        attribute_columns = [('name','somebody'+str(i)), ('address','somewhere'+str(i)), ('age',i)]
        row = Row(primary_key, attribute_columns)
        condition = Condition(RowExistenceExpectation.IGNORE)
        item = PutRowItem(row, condition)
        put_row_items.append(item)

	## Add rows by UpdateRow.
    for i in range(10, 20):
        primary_key = [('gid',i), ('uid',i+1)]
        attribute_columns = {'put': [('name','somebody'+str(i)), ('address','somewhere'+str(i)), ('age',i)]}
        row = Row(primary_key, attribute_columns)
        condition = Condition(RowExistenceExpectation.IGNORE, SingleColumnCondition("age", i, ComparatorType.EQUAL))
        item = UpdateRowItem(row, condition)
        put_row_items.append(item)

	## Add rows by DeleteRow.
    delete_row_items = []
    for i in range(10, 20):
        primary_key = [('gid',i), ('uid',i+1)]
        row = Row(primary_key)
        condition = Condition(RowExistenceExpectation.IGNORE)
        item = DeleteRowItem(row, condition)
        delete_row_items.append(item)

	# Construct a batch write request.
    request = BatchWriteRowRequest()
    request.add(TableInBatchWriteRowItem(table_name, put_row_items))
    request.add(TableInBatchWriteRowItem('notExistTable', delete_row_items))
	
	# Call the batch_write_row method to perform a batch write operation. If request parameters are incorrect, an exception is thrown. If data fails to be written to certain rows, then internal items fail, but no exception is thrown.
	try:
    	result = client.batch_write_row(request)
		print ('Result status: %s'%(result.is_all_succeed()))

		## Check the results of PutRow.
    	print ('check first table\'s put results:')
    	succ, fail = result.get_put()
    	for item in succ:
        	print ('Put succeed, consume %s write cu.' % item.consumed.write)
    	for item in fail:
        	print ('Put failed, error code: %s, error message: %s' % (item.error_code, item.error_message))

		## Check the results of UpdateRow.
    	print ('check first table\'s update results:')
    	succ, fail = result.get_update()
    	for item in succ:
        	print ('Update succeed, consume %s write cu.' % item.consumed.write)
    	for item in fail:
        	print ('Update failed, error code: %s, error message: %s' % (item.error_code, item.error_message))

		## Check the results of DeleteRow.
    	print ('check second table\'s delete results:')
    	succ, fail = result.get_delete()
    	for item in succ:
        	print ('Delete succeed, consume %s write cu.' % item.consumed.write)
    	for item in fail:
        	print ('Delete failed, error code: %s, error message: %s' % (item.error_code, item.error_message)) 
	# The client is abnormal, which is typically due to incorrect parameters or a network error.
	except OTSClientError as e:
		print "get row failed, http_status:%d, error_message:%s" % (e.get_http_status(), e.get_error_message())
	# The server is abnormal, which is typically due to incorrect parameters or a network error.
	except OTSServiceError as e:
		print "get row failed, http_status:%d, error_code:%s, error_message:%s, request_id:%s" % (e.get_http_status(), e.get_error_code(), e.get_error_message(), e.get_request_id())

```

Obtain code details at [BatchWriteRow@GitHub](https://github.com/aliyun/aliyun-tablestore-python-sdk/blob/master/examples/batch_write_row.py).

## GetRange { .section}

Reads data within the specified primary key range.

**API**

```language-python
        """
        Description: This operation reads data from multiple rows in the specified range.
        ``table_name`` is the name of the table.
        ``direction`` indicates the direction of the range; string format, values: 'FORWARD' or 'BACKWARD'.
        ``inclusive_start_primary_key`` is the primary key to start from (inclusive).
        ``exclusive_end_primary_key`` is the primary key to end on (exclusive).
        ``columns_to_get`` is an optional parameter that indicates the names of the columns to read in list format. If not entered, all columns are read.
        ``limit`` is an optional parameter that indicates the maximum number of rows to read. If not entered, the number of rows to read is not limited.
        ``column_filter`` is an optional parameter, which indicates reading the rows that meet specified conditions.
        ``max_version`` is an optional parameter, which indicates the maximum number of returned versions. max_version and time_range are mutually exclusive.
        ``time_range`` is an optional parameter, which indicates the range of returned versions. time_range and max_version are mutually exclusive.
        ``start_column`` is an optional parameter, which indicates the start column of the current read operation in the case of wide row reading.
        ``end_column`` is an optional parameter, which indicates the end column of the current read operation in the case of wide row reading.
        ``token`` is an optional parameter, which indicates the location of the start column of the current read operation in the case of wide row reading. The content is encoded in binary format and comes from the returned results of the previous request.

        Return: The results list for data that meet the conditions.
        ``consumed`` indicates the CapacityUnits consumed by the operation. It is an instance of the tablestore.metadata.CapacityUnit class.
        ``next_start_primary_key`` indicates the primary key column value from which to start the next get_range operation; type: dict.
        ``row_list`` indicates the list of returned row data, in the format of [Row, ...].  
        """
   def get_range(self, table_name, direction,
                  inclusive_start_primary_key,
                  exclusive_end_primary_key,
                  columns_to_get=None,
                  limit=None,
                  column_filter=None,
                  max_version=None,
                  time_range=None,
                  start_column=None,
                  end_column=None,
                  token = None):

```

**Examples**

Read data in the specified range.

```language-python
	# Start primary key in the query range
	inclusive_start_primary_key = [('uid',INF_MIN), ('gid',INF_MIN)]
	
	# End primary key in the query range
    exclusive_end_primary_key = [('uid',INF_MAX), ('gid',INF_MAX)]
	
	# Query all columns
    columns_to_get = []
	
	# A maximum of 90 results are returned at a time. If 100 results are returned in total and the limit is set to 90 during initial query, then a maximum of 90 results and a minimum of 0 results may be returned for the first time, but next_start_primary_key is not None.
    limit = 90

	# Set the filter
    cond = CompositeColumnCondition(LogicalOperator.AND)
    cond.add_sub_condition(SingleColumnCondition("address", 'China', ComparatorType.EQUAL))
    cond.add_sub_condition(SingleColumnCondition("age", 50, ComparatorType.LESS_THAN))

	try:
		# Call the get_range interface
    	consumed, next_start_primary_key, row_list, next_token  = client.get_range(
                table_name, Direction.FORWARD,
                inclusive_start_primary_key, exclusive_end_primary_key,
                columns_to_get,
                limit,
                column_filter = cond,
                max_version = 1
    )

    	all_rows = []
    	all_rows.extend(row_list)
		
		# If the value of next_start_primary_key is not null, more data exists and reading continues.
    	while next_start_primary_key is not None:
        	inclusive_start_primary_key = next_start_primary_key
        	consumed, next_start_primary_key, row_list, next_token = client.get_range(
                table_name, Direction.FORWARD,
                inclusive_start_primary_key, exclusive_end_primary_key,
                columns_to_get, limit,
                column_filter = cond,
                max_version = 1
        )
        	all_rows.extend(row_list)

		# Print the primary key and attribute columns
    	for row in all_rows:
        	print (row.primary_key, row.attribute_columns)
    	print ('Total rows: ', len(all_rows))
	# The client is abnormal, which is typically due to incorrect parameters or a network error.
	except OTSClientError as e:
		print "get row failed, http_status:%d, error_message:%s" % (e.get_http_status(), e.get_error_message())
	# The server is abnormal, which is typically due to incorrect parameters or a network error.
	except OTSServiceError as e:
		print "get row failed, http_status:%d, error_code:%s, error_message:%s, request_id:%s" % (e.get_http_status(), e.get_error_code(), e.get_error_message(), e.get_request_id())

```

Obtain code details at: [GetRange@GitHub](https://github.com/aliyun/aliyun-tablestore-python-sdk/blob/master/examples/get_range.py).

