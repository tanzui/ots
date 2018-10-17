# Table-level operations {#reference4792 .reference}

The Table Store SDK provides the following APIs for manipulating tables: CreateTable, ListTable, UpdateTable, DescribeTable, and DeleteTable.

## CreateTable { .section}

Creates a table based on the given table structure information.

When creating a table in Table Store, you must specify the table’s primary keys. A primary key contains one to four primary key columns. Each primary key column has a name and a type.

**API**

```language-python
       Description: This operation creates a table based on the entered table information.

        ``table_meta`` is an instance of the ``tablestore.metadata.TableMeta`` class. It contains the table name and primary key schema.
        For more information, see the documentation for the ``TableMeta`` class. After a table is created, it generally takes about 1 minute to load the partitions. After that, you can perform table operations.
        ``table_options`` is an instance of the ``tablestore.metadata.TableOptions`` class. It contains time_to_live, max_version, and max_time_deviation.
        ``reserved_throughput`` is an instance of the ``tablestore.metadata.ReservedThroughput`` class. It indicates the reserved read/write throughput.

        Return: Null.
		
		def create_table(self, table_meta, reserved_throughput):

```

**Note:** After a table is created in Table Store, it takes several seconds to load the table. Do not perform any operations during this time.

**Example**

Create a table with two primary key columns, TTL of one year \(that is, 60 \* 60 \* 24 \* 365 = 31536000 seconds\), MaxVersions of 3, MaxTimeDeviation of one day \(that is, 86400 seconds\), and a reserved read/write throughput of \(0,0\).

```language-python
	# Create a schema for the primary key columns, including the quantity, names, and types of primary keys.
	# The first primary key column (sharding column) is named pk0 and of the integer type.
	# The second primary key column is named pk1 and of the integer type. You can also use STRING or BINARY.
	schema_of_primary_key = [('pk0', 'INTEGER'), ('pk1', 'INTEGER')]

	# Create a tableMeta class based on the table name and primary key columns
	table_meta = TableMeta('SampleTable', schema_of_primary_key)
	
	# Create TableOptions. Set TTL to 31536000 seconds (The data is cleared automatically when expired), MaxVersions to 3, and MaxTimeDeviation to one day.
	table_options = TableOptions(31536000, 3, 86400)

	# Set the reserved read throughput and reserved write throughput to 0
	reserved_throughput = ReservedThroughput(CapacityUnit(0, 0))

	# Call the create_table API of the client. The execution is successful if no exception is thrown; otherwise, the execution fails
	try:
		ots_client.create_table(table_meta, table_options, reserved_throughput)
		print "create table succeeded"
	# Handle exceptions
	except Exception:
		print "create table failed."

```

For code details, see [CreateTable@GitHub](https://github.com/aliyun/aliyun-tablestore-python-sdk/blob/master/examples/table_operations.py).

## ListTable { .section}

Obtains the names of all tables under the current instance.

**API**

```language-python
        """
        Description: This operation lists all table names.
        Return: Table name list.
        ``table_list`` indicates the table name list; type: tuple. For example: ('MyTable1', 'MyTable2').
		"""
		def list_table(self):

```

**Example**

Obtain the names of all tables under an instance.

```language-python
	try:
		list_response = ots_client.list_table()
		print 'table list：'
		for table_name in list_response:
    		print table_name
		print "list table succeeded"
	except Exception:
		print "list table failed."

```

For code details, see [ListTable@GitHub](https://github.com/aliyun/aliyun-tablestore-python-sdk/blob/master/examples/table_operations.py).

## UpdateTable { .section}

Updates the reserved read or write throughput value of the specified table.

**API**

```language-python
	"""
        Description: This operation updates table attributes. Now, this function can only modify reserved read/write throughput.
        ``table_name`` is the name of the table.
		``table_options`` is an instance of the ``tablestore.metadata.TableOptions`` class. It contains time_to_live, max_version, and max_time_deviation.
        ``reserved_throughput`` is an instance of the ``ots2.metadata.ReservedThroughput`` class. This indicates the reserved read/write throughput.

        Return: The last time that the reserved throughput was raised or lowered for this table and the number of times it was lowered today.

        ``update_table_response`` indicates the update results. This is an instance of the ots2.metadata.UpdateTableResponse class.
		"""
		def update_table(self, table_name, table_options, reserved_throughput):

```

**Example**

Update the Max Versions to 5.

```language-python
        # Update the reserved read throughput to 0 and the reserved write throughput to 0
        reserved_throughput = ReservedThroughput(CapacityUnit(0, 0))
		
		# Create TableOptions. Set TTL to 31536000 seconds (The data is cleared automatically when expired), MaxVersions to 3, and MaxTimeDeviation to one day.
		table_options = TableOptions(31536000, 5, 86400)

        try:
            # Call the API to update the reserved read/write throughput of the table
            ots_client.update_table('SampleTable', reserved_throughput)

            # The execution is successful if no exception is thrown
            print "update table succeeded"
        except Exception:
            # The call fails if an exception is thrown, and an error message is printed
            print "update table failed"

```

For code details, see [UpdateTable@GitHub](https://github.com/aliyun/aliyun-tablestore-python-sdk/blob/master/examples/table_operations.py).

## DescribeTable { .section}

Queries the structure information and the reserved read/write throughput value of the specified table.

**API**

```language-python
        """
        Description: This operation gets the table description.
        ``table_name`` is the name of the table.
        Return: Table description.
        ``describe_table_response`` indicates the table description. This is an instance of the ots2.metadata.DescribeTableResponse class.
		"""
		def describe_table(self, table_name):

```

**Example**

Obtain the descriptive information of a table.

```language-python
	try:
		describe_response = ots_client.describe_table('myTable')
		# If no exception is thrown, execution is successful and the following table information is printed.
		print "describe table succeeded."
		print ('TableName: %s' % describe_response.table_meta.table_name)
		print ('PrimaryKey: %s' % describe_response.table_meta.schema_of_primary_key)
		print ('Reserved read throughput: %s' % describe_response.reserved_throughput_details.capacity_unit.read)
		print ('Reserved write throughput: %s' % describe_response.reserved_throughput_details.capacity_unit.write)
		print ('Last increase throughput time: %s' % describe_response.reserved_throughput_details.last_increase_time)
		print ('Last decrease throughput time: %s' % describe_response.reserved_throughput_details.last_decrease_time)
		print ('table options\'s time to live: %s' % describe_response.table_options.time_to_live)
		print ('table options\'s max version: %s' % describe_response.table_options.max_version)
		print ('table options\'s max_time_deviation: %s' % describe_response.table_options.max_time_deviation) 
	except Exception:
		# If an exception is thrown, the operation fails and the exception is processed.
		print "describe table failed."

```

For code details, see [DescribeTable@GitHub](https://github.com/aliyun/aliyun-tablestore-python-sdk/blob/master/examples/table_operations.py).

## DeleteTable { .section}

Deletes the specified table under an instance.

**API**

```language-python
        """
        Description: This operation deletes the table corresponding to the specified table name.
        ``table_name`` is the name of the table.
        Return: Null.
		"""
		def delete_table(self, table_name):

```

**Example**

Delete a table.

```language-python
try:
	# Call the API to delete the table SampleTable
    ots_client.delete_table('SampleTable')
	# If no exception is thrown, the operation is successful
	print "delete table succeeded"
	# If an exception is thrown, the operation fails and the exception is processed
except Exception:
    print "delete table failed"

```

For code details, see [DeleteTable@GitHub](https://github.com/aliyun/aliyun-tablestore-python-sdk/blob/master/examples/table_operations.py).

