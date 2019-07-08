# Delete a table {#concept_exl_pnf_4gb .concept}

You can call the DeleteTable operation to delete a table from the current instance.

**Method**

```language-python
        """
        Description: You can call this method to delete a table based on the table name.
        ``table_name`` specifies the name of the table.
        Return value: none.
		"""
		def delete_table(self, table_name):

```

**Example**

The following code provides an example of how to delete a table.

```language-python
try:
	# Call the delete_table method to delete the table named SampleTable.
    ots_client.delete_table('SampleTable')
	# If no exception is thrown, the operation is successful
	print "delete table succeeded"
	# If an exception is thrown, the operation fails. Handle the exception.
except Exception:
    print "delete table failed"

```

**Note:** You can obtain the full sample code at [deleteTable@GitHub](https://github.com/aliyun/aliyun-tablestore-go-sdk/blob/master/sample/TableOperation.go).

