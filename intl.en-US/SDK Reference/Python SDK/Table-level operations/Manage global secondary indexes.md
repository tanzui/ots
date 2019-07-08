# Manage global secondary indexes {#concept_261871 .concept}

## Create a global secondary index {#section_x8p_ch2_bxx .section}

You can create a global secondary index when creating a table or add a global secondary index to an existing table. You must use defined\_columns to specify the fields that can be used by a global secondary index when creating a table.

## Create a global secondary index when creating a table {#section_dcu_f9n_3lq .section}

``` {#codeblock_l93_7d1_vaf}
schema_of_primary_key = [('gid', 'INTEGER'), ('uid', 'STRING')]
defined_columns = [('i', 'INTEGER'), ('bool', 'BOOLEAN'), ('d', 'DOUBLE'), ('s', 'STRING'), ('b', 'BINARY')]
table_meta = TableMeta(table_name, schema_of_primary_key, defined_columns)
table_option = TableOptions(-1, 1)
reserved_throughput = ReservedThroughput(CapacityUnit(0, 0))
secondary_indexes = [
    SecondaryIndexMeta('index1', ['i', 's'], ['gid', 'uid', 'bool', 'b', 'd']),
    ]
client.create_table(table_meta, table_option, reserved_throughput, secondary_indexes)
			
```

## Add a global secondary index to an existing table {#section_qu8_oyd_hc9 .section}

``` {#codeblock_5lo_c1a_wr5}
index_meta = SecondaryIndexMeta('index2', ['i', 's'], ['gid', 'uid', 'bool', 'b', 'd'])
client.create_secondary_index(table_name, index_meta)
```

## Delete a global secondary index {#section_w3y_nn8_18g .section}

``` {#codeblock_i2m_qey_6bz}
client.delete_secondary_index(table_name, 'index1')
```

