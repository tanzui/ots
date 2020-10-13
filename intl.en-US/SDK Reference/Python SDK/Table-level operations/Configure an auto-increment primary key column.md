# Configure an auto-increment primary key column

## Prerequisites

The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/Python SDK/Initialization.md).

## Examples

When you use auto-increment primary key columns, you can call the CreateTable, PutRow, UpdateRow, or BatchWriteRow operation.

1.  Create a table

    To create an auto-increment primary key column when you create a table, you must set the attribute of the primary key column to PK\_AUTO\_INCR.

    ```
    from tablestore import *
    
    table_name = 'OTSPkAutoIncrSimpleExample'
    
    def create_table(client):
        # Create a table that includes the following two INTEGER primary keys: gid and uid. Set uid to the auto-increment column.
        schema_of_primary_key = [('gid', 'INTEGER'), ('uid', 'INTEGER', PK_AUTO_INCR)]
        table_meta = TableMeta(table_name, schema_of_primary_key)
        table_options = TableOptions()
        reserved_throughput = ReservedThroughput(CapacityUnit(0, 0))
        client.create_table(table_meta, table_options, reserved_throughput)
        print ('Table has been created.')
    ```

2.  Write data

    When you write data to a table, you do not need to specify a specific value for the auto-increment column. Instead, you need only to set the value of the auto-increment column to PK\_AUTO\_INCR.

    ```
    from tablestore import *
    
    table_name = 'OTSPkAutoIncrSimpleExample'
    
    def put_row(client):
        # Write data to the primary keys: Set the value of gid to 1 and set uid to the auto-increment column. You must specify a value for the uid column. Otherwise, an error is returned.
        primary_key = [('gid',1), ('uid', PK_AUTO_INCR)]
        attribute_columns = [('name','John'), ('mobile',15100000000), ('address','China'), ('age',20)]
        row = Row(primary_key, attribute_columns)
    
        # Write data to attribute columns.
        row.attribute_columns = [('name','John'), ('mobile',15100000000), ('address','China'), ('age',25)]
        consumed, return_row = client.put_row(table_name, row)
        print ('Write succeed, consume %s write cu.' % consumed.write)
    
        consumed, return_row = client.put_row(table_name, row, return_type = ReturnType.RT_PK)
        print ('Write succeed, consume %s write cu.' % consumed.write)
        print ('Primary key:%s' % return_row.primary_key)
    ```


