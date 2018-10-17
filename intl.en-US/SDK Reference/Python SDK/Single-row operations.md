# Single-row operations {#reference7829 .reference}

The Table Store SDK provides the following single-row operation APIs: PutRow, GetRow, UpdateRow, and DeleteRow.

## PutRow { .section}

Inserts data into a specified row.

**API**

```language-python
        """
        Description: This operation writes a single row of data. CapacityUnit consumed by the operation is returned.
        ``table_name`` is the name of the table.
        ``row`` indicates row data, including primary key columns and attribute columns.
        ``condition`` indicates the conditions to be checked before an operation is executed. If the conditions are met, the operation is executed. It is an instance of the tablestore.metadata.Condition class. Currently, this function only supports row existence checks. Check conditions include: 'IGNORE', 'EXPECT_EXIST', and 'EXPECT_NOT_EXIST'.
        ``return_type`` indicates the type of the returned value. It is an instance of the tablestore.metadata.ReturnType class. Currently, only return of PrimaryKey is supported, which is typically used in automatic increment of primary key columns.
		
        Return: CapacityUnits consumed by the operation, and the row data to be returned.
		``consumed`` indicates the CapacityUnits consumed. It is an instance of the tablestore.metadata.CapacityUnit class.
        ``return_row`` indicates the row data returned, which may include the primary key and attribute columns.
		"""
		def put_row(self, table_name, row, condition = None, return_type = None)

```

**Examples**

Insert a data row.

```language-python
	## The first primary key column of the primary key is gid with an integral value of 1. The second primary key column is uid with an integral value of 101.
    primary_key = [('gid',1), ('uid',101)]
	
	## Four attribute columns exist:
	##                        The first attribute column is "name" with the string value "John". The version is not specified, and the current system time is used as the version.
	##                        The second attribute column is "mobile" with the integral value 15100000000. The version is not specified, and the current system time is used as the version.
	##                        The third attribute column is "address" with the binary value "China". The version is not specified, and the current system time is used as the version.
	##                        The fourth column is "age" with the value 29.7. The version is 1498184687.
	attribute_columns = [('name','John'), ('mobile',15100000000),('address', bytearray('China')),('female', False), ('age', 29.7, 1498184687000)] 
	
	## Construction of Row using primary_key and attribute_columns
    row = Row(primary_key, attribute_columns)
	
    # Row condition: The expected row does not exist. If the expected row exists, the error "Condition Update Failed" is returned.
    condition = Condition(RowExistenceExpectation.EXPECT_NOT_EXIST)
	
	try :
		# Call the put_row method. If ReturnType is not specified, return_row is None.
    	consumed, return_row = client.put_row(table_name, row, condition)
	
		# The write CU consumed by this request is printed.
    	print ('put row succeed, consume %s write cu.' % consumed.write)
	# The client is abnormal, which is typically due to incorrect parameters or a network error.
	except OTSClientError as e:
		print "put row failed, http_status:%d, error_message:%s" % (e.get_http_status(), e.get_error_message())
	# The server is abnormal, which is typically due to incorrect parameters or a network error.
	except OTSServiceError as e:
		print "put row failed, http_status:%d, error_code:%s, error_message:%s, request_id:%s" % (e.get_http_status(), e.get_error_code(), e.get_error_message(), e.get_request_id())

```

**Note:** 

-   RowExistenceExpectation.IGNORE indicates that new data is inserted no matter whether the specified row exists or not. If the inserted data is the same as the existing data, the existing data is overwritten.

-   RowExistenceExpectation.EXPECT\_EXIST indicates that new data is inserted only when the specified row exists. The existing data is overwritten.

-   RowExistenceExpectation.EXPECT\_NOT\_EXIST indicates that data is inserted only when the specified row does not exist.

-   In the preceding sample program, the version of the attribute column “age” is 1498184687000, and the value is June, 23, 2017. If the current time minus the value of max\_time\_deviation \(specified during table creation\) is greater than 1498184687000, the PutRow operation is disabled.
-   If the operation is successful, no exception is thrown; if the operation fails, an exception is thrown.


Obtain code details at [PutRow@GitHub](https://github.com/aliyun/aliyun-tablestore-python-sdk/blob/master/examples/put_row.py).

## GetRow { .section}

Reads a single data row based on a given Primary Key.

**API**

```language-python
       """
        Description: This operation reads a single row of data.
        ``table_name`` is the name of the table.
        ``primary_key`` indicates the primary key; type: dict.
        ``columns_to_get`` is an optional parameter that indicates the names of the columns to read in list format. If not entered, all columns are read.
        ``column_filter`` is an optional parameter, which indicates reading the row that meets specified conditions.
        ``max_version`` is an optional parameter, which indicates the maximum number of read versions.
        ``time_range`` is an optional parameter, which indicates reading versions within the specified range or reading the specified version. time_range and max_version are mutually exclusive.

        Return: The CapacityUnits consumed by this operation, the primary key columns, and attribute columns.
        ``consumed`` indicates the CapacityUnits consumed. It is an instance of the tablestore.metadata.CapacityUnit class.
        ``return_row`` indicates row data, including primary key columns and attribute columns of the list type, for example, [('PK0',value0), ('PK1',value1)].
        ``next_token`` indicates the location of the next read operation in the case of wide row reading. The value is encoded in binary format.
		"""
		def get_row(self, table_name, primary_key, columns_to_get=None,
                column_filter=None, max_version=None, time_range=None,
                start_column=None, end_column=None, token=None):

```

**Examples**

Read a data row.

```language-python
	# The first column of the primary key is uid with the integral value 1. The second column is gid with the integral value 101.
    primary_key = [('uid',1), ('gid',101)]
	
	# Attribute columns to be returned: name, growth, and type If columns_to_get is [], all attribute columns are returned.
    columns_to_get = ['name', 'growth', 'type'] 
	
	# Add column filter: This row is returned if the value of the growth column is not 0.9.
    cond = CompositeColumnCondition(LogicalOperator.AND)
    cond.add_sub_condition(SingleColumnCondition("growth", 0.9, ComparatorType.NOT_EQUAL))
    cond.add_sub_condition(SingleColumnCondition("name", 'Hangzhou', ComparatorType.EQUAL))
	
	try:
		# Call the get_row query interface. The last parameter 1 indicates that the value of only one version needs to be returned.
    	consumed, return_row, next_token = client.get_row(table_name, primary_key, columns_to_get, cond, 1)
    	print ('Read succeed, consume %s read cu.' % consumed.read)
    	print ('Value of primary key: %s' % return_row.primary_key)
    	print ('Value of attribute: %s' % return_row.attribute_columns)
    	for att in return_row.attribute_columns:
			# Print the key, value, and version of each column.
        	print ('name:%s\tvalue:%s\ttimestamp:%d' % (att[0], att[1], att[2]))
	# The client is abnormal, which is typically due to incorrect parameters or a network error.
	except OTSClientError as e:
		print "get row failed, http_status:%d, error_message:%s" % (e.get_http_status(), e.get_error_message())
	# The server is abnormal, which is typically due to incorrect parameters or a network error.
	except OTSServiceError as e:
		print "get row failed, http_status:%d, error_code:%s, error_message:%s, request_id:%s" % (e.get_http_status(), e.get_error_code(), e.get_error_message(), e.get_request_id())

```

**Note:** If you query a data row, the system returns the data in all columns of the row. You can use the columns\_to\_get parameter to read the data in specified columns. For example, the system only returns the data in col0 and col1 if col0 and col1 are inserted into columns\_to\_get.

Obtain code details at [GetRow@GitHub](https://github.com/aliyun/aliyun-tablestore-python-sdk/blob/master/examples/get_row.py).

## UpdateRow { .section}

Updates the data of the specified row. If the row does not exist, a new row is added. If the row exists, the values of the specified columns are added, modified, or deleted based on the request content.

**API**

```language-python
        """
        Description: This operation updates a single row of data.
        ``table_name`` is the name of the table.
        ``row`` indicates the updated row data, including primary key columns (of the list type) and attribute columns (of the dict type).
        ``condition`` indicates the conditions to be checked before an operation is executed. If the conditions are met, the operation is executed. It is an instance of the tablestore.metadata.Condition class.
        Currently, this function only supports row existence checks. Check conditions include: 'IGNORE', 'EXPECT_EXIST', and 'EXPECT_NOT_EXIST'.
        ``return_type`` indicates the type of the returned value. It is an instance of the tablestore.metadata.ReturnType class. Currently, only return of PrimaryKey is supported, which is typically used in automatic increment of primary key columns.
        Return: CapacityUnits consumed by the operation, and the row data to be returned (which is indicated by return_row).
        consumed indicates the CapacityUnits consumed. It is an instance of the tablestore.metadata.CapacityUnit class.
        return_row indicates the row data to be returned.
        """
        def update_row(self, table_name, row, condition, return_type = None)

```

**Examples**

Update a data row.

```language-python
	# The first column of the primary key is uid with the integral value 1. The second column is gid with the integral value 101.
    primary_key = [('uid',1), ('gid',101)]
	
	# The update contains three parts: PUT, DELETE, and DELETE_ALL.
	# PUT: Add two columns. The first column is "name" and the value is David; the second column is "address" and the value is Hongkong.
	# DELETE: Delete the value of the address column with the version 1488436949003.
	# DELETE_ALL: Delete the values of all versions in the "mobile" and "age" columns.
    update_of_attribute_columns = {
        'PUT' : [('name','David'), ('address','Hongkong')],
        'DELETE' : [('address', None, 1488436949003)],
        'DELETE_ALL' : [('mobile'), ('age')], 
    }
    row = Row(primary_key, update_of_attribute_columns)
	
	# The row condition is Ignore, indicating that data is updated regardless of whether the specified row exists.
    condition = Condition(RowExistenceExpectation.IGNORE, SingleColumnCondition("age", 20, ComparatorType.EQUAL)) # update row on\
ly when this row is exist

	try:
    	consumed, return_row = client.update_row(table_name, row, condition)
	# The client is abnormal, which is typically due to incorrect parameters or a network error.
	except OTSClientError as e:
		print "update row failed, http_status:%d, error_message:%s" % (e.get_http_status(), e.get_error_message())
	# The server is abnormal, which is typically due to incorrect parameters or a network error.
	except OTSServiceError as e:
		print "update row failed, http_status:%d, error_code:%s, error_message:%s, request_id:%s" % (e.get_http_status(), e.get_error_code(), e.get_error_message(), e.get_request_id())

```

Obtain code details at [UpdateRow@GitHub](https://github.com/aliyun/aliyun-tablestore-python-sdk/blob/master/examples/update_row.py).

## DeleteRow { .section}

**API**

```language-python
        """
        Description: This operation deletes a single row of data.
        ``table_name`` is the name of the table.
        ``row`` indicates row data, which contains only the primary key in the case of delete_row.
        ``condition`` indicates the conditions to be checked before an operation is executed. If the conditions are met, the operation is executed. It is an instance of the tablestore.metadata.Condition class.
        Currently, this function only supports row existence checks. Check conditions include: 'IGNORE', 'EXPECT_EXIST', and 'EXPECT_NOT_EXIST'.
        Return: CapacityUnits consumed by the operation, and the row data to be returned (which is indicated by return_row). 
        consumed indicates the CapacityUnits consumed. It is an instance of the tablestore.metadata.CapacityUnit class.
        return_row indicates the row data to be returned.
        """
		def delete_row(self, table_name, row, condition, return_type = None): 

```

**Examples**

Delete a data row.

```language-python
    primary_key = [('gid',1), ('uid','101')]
    row = Row(primary_key)
	try:
    	consumed, return_row = client.delete_row(table_name, row, None)
	# The client is abnormal, which is typically due to incorrect parameters or a network error.
	except OTSClientError as e:
		print "update row failed, http_status:%d, error_message:%s" % (e.get_http_status(), e.get_error_message())
	# The server is abnormal, which is typically due to incorrect parameters or a network error.
	except OTSServiceError as e:
		print "update row failed, http_status:%d, error_code:%s, error_message:%s, request_id:%s" % (e.get_http_status(), e.get_error_code(), e.get_error_message(), e.get_request_id())	
    print ('Delete succeed, consume %s write cu.' % consumed.write) 

```

Obtain code details at [DeleteRow@GitHub](https://github.com/aliyun/aliyun-tablestore-python-sdk/blob/master/examples/delete_row.py).

