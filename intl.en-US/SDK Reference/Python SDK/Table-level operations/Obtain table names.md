# Obtain table names {#concept_nmn_3nf_4gb .concept}

You can call the ListTable operation to obtain the names of all tables that are created in the current instance.

**Method**

```language-python
        """
        Description: You can call this method to obtain all table names.
        Return value: the table name list.
        ``table_list`` indicates the table name list of the tuple type. For example: ('MyTable1', 'MyTable2').
		"""
		def list_table(self):

```

**Example**

The following code provides an example of how to obtain the names of all tables in an instance.

```language-python
	try:
		list_response = ots_client.list_table()
		print 'table list:'
		for table_name in list_response:
    		print table_name
		print "list table succeeded"
	except Exception:
		print "list table failed."

```

You can obtain the full sample code at [ListTable@GitHub](https://github.com/aliyun/aliyun-tablestore-python-sdk/blob/master/examples/table_operations.py).

