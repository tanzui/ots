# Java SDK for global secondary indexes {#concept_r1l_4db_ffb .concept}

In this section, you can call the createTable method and the scanFromIndex method in the Java SDK to perform the following operations.

-   You can create a base table and an index on this base table at the same time.

    ``` {#codeblock_86n_amw_xkb}
    private static void createTable(SyncClient client) {
        TableMeta tableMeta = new TableMeta(TABLE_NAME);
        tableMeta.addPrimaryKeyColumn(new PrimaryKeySchema(PRIMARY_KEY_NAME_1, PrimaryKeyType.STRING)); // You can specify a primary key for a base table.
        tableMeta.addPrimaryKeyColumn(new PrimaryKeySchema(PRIMARY_KEY_NAME_2, PrimaryKeyType.INTEGER)); // Set primary key for the base table
        tableMeta.addDefinedColumn(new DefinedColumnSchema(DEFINED_COL_NAME_1, DefinedColumnType.STRING)); // You can specify a pre-defined attribute for the base table.
        tableMeta.addDefinedColumn(new DefinedColumnSchema(DEFINED_COL_NAME_2, DefinedColumnType.INTEGER)); // You can specify a pre-defined attribute for the base table.
        tableMeta.addDefinedColumn(new DefinedColumnSchema(DEFINED_COL_NAME_3, DefinedColumnType.INTEGER)); // You can specify a pre-defined attribute for the base table.
    
        int timeToLive = -1; // You can specify -1 as the Time To Live (TTL) value so the data never expires.
        int maxVersions = 1; // The maximum version number. You can only specify 1 as the version value when a base table have one or more indexes.
    
        TableOptions tableOptions = new TableOptions(timeToLive, maxVersions);
    
        ArrayList<IndexMeta> indexMetas = new ArrayList<IndexMeta>();
        IndexMeta indexMeta = new IndexMeta(INDEX_NAME); // You can create an index.
        indexMeta.addPrimaryKeyColumn(DEFINED_COL_NAME_1); // You can specify DEFINED_COL_NAME_1 of the base table as an index primary key.
        indexMeta.addDefinedColumn(DEFINED_COL_NAME_2); // You can specify DEFINED_COL_NAME_2 of the base table as an index primary key.
        indexMetas.add(indexMeta); // You can add the index to the base table.
    
        CreateTableRequest request = new CreateTableRequest(tableMeta, tableOptions, indexMetas); // You can create the base table.
    
        client.createTable(request);
    }
    ```

-   You can create an index on a base table.

    ``` {#codeblock_wg4_uz2_b7u}
    private static void createIndex(SyncClient client) {
        IndexMeta indexMeta = new IndexMeta(INDEX_NAME); // You can create an index.
        indexMeta.addPrimaryKeyColumn(DEFINED_COL_NAME_2); // You can specify DEFINED_COL_NAME_2 as the first attribute of an index primary key.
        indexMeta.addPrimaryKeyColumn(DEFINED_COL_NAME_1); // You can specify DEFINED_COL_NAME_1 as the second attribute of an index primary key.
        CreateIndexRequest request = new CreateIndexRequest(TABLE_NAME, indexMeta, false); // You can create an index on a base table.
        client.createIndex(request); // You can create an index.
    }
    ```

    **Note:** At the moment, existing data in the base table will not be copied to the index when you create an index on a base table. The newly created index only includes incremental data after you create this index. For more information about incremental data, contact Table Store technical support with DingTalk.

-   You can delete an index.

    ``` {#codeblock_1tp_5rc_fma}
    private static void deleteIndex(SyncClient client) {
        DeleteIndexRequest request = new DeleteIndexRequest(TABLE_NAME, INDEX_NAME); // You can specify the names of a base table and an index.
        client.deleteIndex(request); // You can delete an index.
    }
    ```

-   You can read data from an index.

    If an index includes an attribute that will be returned in results, you can directly retrieve data from the index.

    ``` {#codeblock_qbi_xda_x5i}
    private static void scanFromIndex(SyncClient client) {
        RangeRowQueryCriteria rangeRowQueryCriteria = new RangeRowQueryCriteria(INDEX_NAME); // You can specify the name of an index.
    
        // You can specify the start primary key.
        PrimaryKeyBuilder startPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        startPrimaryKeyBuilder.addPrimaryKeyColumn(DEFINED_COL_NAME_1, PrimaryKeyValue.INF_MIN); // You can specify the minimum value for an index primary key.
        startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.INF_MIN); // You can specify the minimum value for a base table primary key.
        startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, PrimaryKeyValue.INF_MIN); // You can specify the minimum value for a base table primary key.
        rangeRowQueryCriteria.setInclusiveStartPrimaryKey(startPrimaryKeyBuilder.build());
    
        // You can specify the end primary key.
        PrimaryKeyBuilder endPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        endPrimaryKeyBuilder.addPrimaryKeyColumn(DEFINED_COL_NAME_1, PrimaryKeyValue.INF_MAX); // You can specify the maximum value for an index attribute.
        endPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.INF_MAX); // You can specify the maximum value for a base table primary key.
        endPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, PrimaryKeyValue.INF_MAX); // You can specify the maximum value for a base table primary key.
        rangeRowQueryCriteria.setExclusiveEndPrimaryKey(endPrimaryKeyBuilder.build());
    
        rangeRowQueryCriteria.setMaxVersions(1);
    
        System.out.println("The results returned from an index are as follows:");
        while (true) {
            GetRangeResponse getRangeResponse = client.getRange(new GetRangeRequest(rangeRowQueryCriteria));
            for (Row row : getRangeResponse.getRows()) {
                System.out.println(row);
            }
    
            // If the nextStartPrimaryKey value is not null, you can continue to read data from the base table.
            if (getRangeResponse.getNextStartPrimaryKey() ! = null) {
                rangeRowQueryCriteria.setInclusiveStartPrimaryKey(getRangeResponse.getNextStartPrimaryKey());
            } else {
                break;
            }
        }
    }
    ```

    If an index does not include an attribute that will be returned in results, you must query the base table.

    ``` {#codeblock_g3d_v12_u1e}
    private static void scanFromIndex(SyncClient client) {
        RangeRowQueryCriteria rangeRowQueryCriteria = new RangeRowQueryCriteria(INDEX_NAME); // You can specify the index name.
    
        // You can specify the start primary key.
        PrimaryKeyBuilder startPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        startPrimaryKeyBuilder.addPrimaryKeyColumn(DEFINED_COL_NAME_1, PrimaryKeyValue.INF_MIN); // You can specify the minimum value for an indexed attribute of an index.
        startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.INF_MIN); // You can specify the minimum value for a primary key of a base table.
        startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, PrimaryKeyValue.INF_MIN); // You can specify the minimum value for a primary key of a base table.
        rangeRowQueryCriteria.setInclusiveStartPrimaryKey(startPrimaryKeyBuilder.build());
    
        // You can specify the end primary key.
        PrimaryKeyBuilder endPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        endPrimaryKeyBuilder.addPrimaryKeyColumn(DEFINED_COL_NAME_1, PrimaryKeyValue.INF_MAX); // You can specify the maximum value for an indexed attribute of an index.
        endPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.INF_MAX); // You can specify the maximum value for a base table primary key.
        endPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, PrimaryKeyValue.INF_MAX); // You can specify the maximum value for a base table primary key.
        rangeRowQueryCriteria.setExclusiveEndPrimaryKey(endPrimaryKeyBuilder.build());
    
        rangeRowQueryCriteria.setMaxVersions(1);
    
        while (true) {
            GetRangeResponse getRangeResponse = client.getRange(new GetRangeRequest(rangeRowQueryCriteria));
            for (Row row : getRangeResponse.getRows()) {
                PrimaryKey curIndexPrimaryKey = row.getPrimaryKey();
                PrimaryKeyColumn pk1 = curIndexPrimaryKey.getPrimaryKeyColumn(PRIMARY_KEY_NAME1);
                PrimaryKeyColumn pk2 = curIndexPrimaryKey.getPrimaryKeyColumn(PRIMARY_KEY_NAME2);
                PrimaryKeyBuilder mainTablePKBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
                mainTablePKBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME1, pk1.getValue());
                mainTablePKBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME2, ke2.getValue());
                PrimaryKey mainTablePK = mainTablePKBuilder.build(); // You can specify the index primary keys for a base table.
    
                // You can query a base table.
                SingleRowQueryCriteria criteria = new SingleRowQueryCriteria(TABLE_NAME, mainTablePK);
                criteria.addColumnsToGet(DEFINED_COL_NAME3); // You can read the DEFINED_COL_NAME3 attribute from the base table.
                // You can retrieve the latest data version.
                criteria.setMaxVersions(1);
                GetRowResponse getRowResponse = client.getRow(new GetRowRequest(criteria));
                Row mainTableRow = getRowResponse.getRow();
                System.out.println(row); 
            }
    
            // If the value of nextStartPrimaryKey is not null, you can continue to read data from the base table.
            if (getRangeResponse.getNextStartPrimaryKey() ! = null) {
                rangeRowQueryCriteria.setInclusiveStartPrimaryKey(getRangeResponse.getNextStartPrimaryKey());
            } else {
                break;
            }
        }
    }
    ```


