# Scenarios {#concept_af3_3cb_ffb .concept}

The global secondary index is a new Table Store feature. When you create a table, the primary index is composed of all the primary keys. Table Store uses primary keys to uniquelyidentify each row in a table. However, you need to query a table by attributes, primary keys, or primary keys that are not from the first column in more scenarios. Due to insufficient indexes, you can only fetch the results by scanning the entire table and setting filter conditions. If you obtain few results after querying a table with large data volume, the query can cause excessive consumption of resources.

The Table Store Global secondary index feature is similar to that of [DynamoDB GSI](https://docs.aws.amazon.com/zh_cn/amazondynamodb/latest/developerguide/GSI.html)and [HBase Phoenix](http://phoenix.apache.org/secondary_indexing.html). You can create an index with one or more specified attributes. In addition, you can sort data in the created index by specified attributes. Every data you write to a base table will be asynchronously synchronized to the created index on the base table. You only have to write data to a base table, and can query indexes created on this base table. This configuration greatly improves query performance in most scenarios. For example, you can create a base table for a common phone log query as follows:

|CellNumber|StartTime \(Unix timestamps\)|CalledNumber|Duration|BaseStationNumber|
|:---------|:----------------------------|:-----------|:-------|:----------------|
|123456|1532574644|654321|60|1|
|234567|1532574714|765432|10|1|
|234567|1532574734|123456|20|3|
|345678|1532574795|123456|5|2|
|345678|1532574861|123456|100|2|
|456789|1532584054|345678|200|3|

-   `CellNumber` and `StartTime` are primary keys that represent `a calling number` and `the start time of a call`, respectively.
-   `CalledNumber`, `Duration`, and `BaseStationNumber`are pre-defined attributes that represent `a called number`, `call duration`, and `the base station number`.

When you end a phone call, the call information is written to this table. You can create global secondary indexes on `CalledNumber` and `BaseStationNumber` respectively to meet various query requirements. For more information about how to create an index, see example in [Appendix](intl.en-US/Developer Guide/Global secondary indexes/Appendix.md#).

If you have the following query requirements:

-   You want to fetch the rows where the CellNumber value matches `234567`.

    You can sort data by primary keys in Table Store. In addition, you can call the `getRange` method to scan data sequentially. When you call the `getRange` method, you need to specify `234567` both as the minimum and maximum values for PK0 \(CellNumber\). Meanwhile, you need to specify `0` as the minimum value of PK1 \(StartTime\) and specify `INT_MAX` as the maximum value of PK1. Then you can query the base table.

    ``` {#codeblock_yif_noz_4c0}
    private static void getRangeFromMainTable(SyncClient client, long cellNumber)
    {
        RangeRowQueryCriteria rangeRowQueryCriteria = new RangeRowQueryCriteria(TABLE_NAME);
    
        // You can specify primary keys.
        PrimaryKeyBuilder startPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.fromLong(cellNumber));
        startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, PrimaryKeyValue.fromLong(0));
        rangeRowQueryCriteria.setInclusiveStartPrimaryKey(startPrimaryKeyBuilder.build());
    
        // You can specify primary keys.
        PrimaryKeyBuilder endPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        endPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.fromLong(cellNumber));
        endPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, PrimaryKeyValue.INF_MAX);
        rangeRowQueryCriteria.setExclusiveEndPrimaryKey(endPrimaryKeyBuilder.build());
    
        rangeRowQueryCriteria.setMaxVersions(1);
    
        String strNum = String.format("%d", cellNumber);
        System.out.println("A cell number " + strNum + "makes the following calls:");
        while (true) {
            GetRangeResponse getRangeResponse = client.getRange(new GetRangeRequest(rangeRowQueryCriteria));
            for (Row row : getRangeResponse.getRows()) {
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

-   If you want to fetch the rows where the value of CalledNumber is `123456`.

    Table Store sorts all rows by primary keys. Because CalledNumber is a pre-defined attribute, you cannot directly query a table by this attribute. Therefore, you can query an index that is created on `CalledNumber`.

    `IndexOnBeCalledNumber`:

    |PK0|PK1|PK2|
    |:--|:--|:--|
    |CalledNumber|CellNumber|StartTime|
    |123456|234567|1532574734|
    |123456|345678|1532574795|
    |123456|345678|1532574861|
    |654321|123456|1532574644|
    |765432|234567|1532574714|
    |345678|456789|1532584054|

    **Note:** Table Store will auto complement primary keys of an index. When building this index, Table Store adds all primary keys of a base table to an index created on this base table. Therefore, the index includes three primary keys.

    Because `IndexOnBeCalledNumber` is an index that is created on CalledNumber, you can directly query this index to fetch results.

    ``` {#codeblock_y4h_3or_1fi}
    private static void getRangeFromIndexTable(SyncClient client, long cellNumber) {
        RangeRowQueryCriteria rangeRowQueryCriteria = new RangeRowQueryCriteria(INDEX0_NAME);
    
        // You can specify primary keys.
        PrimaryKeyBuilder startPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        startPrimaryKeyBuilder.addPrimaryKeyColumn(DEFINED_COL_NAME_1, PrimaryKeyValue.fromLong(cellNumber));
        startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.INF_MIN);
        startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, PrimaryKeyValue.INF_MIN);
        rangeRowQueryCriteria.setInclusiveStartPrimaryKey(startPrimaryKeyBuilder.build());
    
        // You can specify primary keys.
        PrimaryKeyBuilder endPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        endPrimaryKeyBuilder.addPrimaryKeyColumn(DEFINED_COL_NAME_1, PrimaryKeyValue.fromLong(cellNumber));
        endPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.INF_MAX);
        endPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, PrimaryKeyValue.INF_MAX);
        rangeRowQueryCriteria.setExclusiveEndPrimaryKey(endPrimaryKeyBuilder.build());
    
        rangeRowQueryCriteria.setMaxVersions(1);
    
        String strNum = String.format("%d", cellNumber);
        System.out.println("A cell number" + strNum + "was called by the following numbers");
        while (true) {
            GetRangeResponse getRangeResponse = client.getRange(new GetRangeRequest(rangeRowQueryCriteria));
            for (Row row : getRangeResponse.getRows()) {
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

-   If you want to fetch the rows where the value of BaseStationNumber matches `002` and the value of StartTime matches `1532574740`.

    This query specifies both`BaseStationNumber` and `StartTime`as conditions. Therefore, you can create a compound index on the `BaseStationNumber` and `StartTime`.

    `IndexOnBaseStation1`:

    |PK0|PK1|PK2|
    |:--|:--|:--|
    |BaseStationNumber|StartTime|CellNumber|
    |001|1532574644|123456|
    |001|1532574714|234567|
    |002|1532574795|345678|
    |002|1532574861|345678|
    |003|1532574734|234567|
    |003|1532584054|456789|

    You can query the `IndexOnBaseStation1`index.

    ``` {#codeblock_xow_96j_3co}
    private static void getRangeFromIndexTable(SyncClient client,
                                               long baseStationNumber,
                                               long startTime) {
        RangeRowQueryCriteria rangeRowQueryCriteria = new RangeRowQueryCriteria(INDEX1_NAME);
    
        // You can specify primary keys.
        PrimaryKeyBuilder startPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        startPrimaryKeyBuilder.addPrimaryKeyColumn(DEFINED_COL_NAME_3, PrimaryKeyValue.fromLong(baseStationNumber));
        startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, PrimaryKeyValue.fromLong(startTime));
        startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.INF_MIN);
        rangeRowQueryCriteria.setInclusiveStartPrimaryKey(startPrimaryKeyBuilder.build());
    
        // You can specify primary keys.
        PrimaryKeyBuilder endPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        endPrimaryKeyBuilder.addPrimaryKeyColumn(DEFINED_COL_NAME_3, PrimaryKeyValue.fromLong(baseStationNumber));
        endPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, PrimaryKeyValue.INF_MAX);
        endPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.INF_MAX);
        rangeRowQueryCriteria.setExclusiveEndPrimaryKey(endPrimaryKeyBuilder.build());
    
        rangeRowQueryCriteria.setMaxVersions(1);
    
        String strBaseStationNum = String.format("%d", baseStationNumber);
        String strStartTime = String.format("%d", startTime);
        System.out.println("All called numbers forwarded by the base station" + strBaseStationNum + " that start from" + strStartTime + "are listed as follows:");
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

-   If you want to fetch the rows where the value of BaseStationNumber `003`matches the StartTime value range from `1532574861` to `1532584054`. Only the Duration will be displayed in the rows.

    In this query, you specify both `BaseStationNumber` and `StartTime` as conditions. Only `Duration` appears in the result set. You can issue a query on the last index, and then fetch Duration by querying the base table.

    ``` {#codeblock_gfb_rle_8qa}
    private static void getRowFromIndexAndMainTable(SyncClient client,
                                                    long baseStationNumber,
                                                    long startTime,
                                                    long endTime,
                                                    String colName) {
        RangeRowQueryCriteria rangeRowQueryCriteria = new RangeRowQueryCriteria(INDEX1_NAME);
    
        // You can specify primary keys.
        PrimaryKeyBuilder startPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        startPrimaryKeyBuilder.addPrimaryKeyColumn(DEFINED_COL_NAME_3, PrimaryKeyValue.fromLong(baseStationNumber));
        startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, PrimaryKeyValue.fromLong(startTime));
        startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.INF_MIN);
        rangeRowQueryCriteria.setInclusiveStartPrimaryKey(startPrimaryKeyBuilder.build());
    
        // You can specify primary keys.
        PrimaryKeyBuilder endPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        endPrimaryKeyBuilder.addPrimaryKeyColumn(DEFINED_COL_NAME_3, PrimaryKeyValue.fromLong(baseStationNumber));
        endPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, PrimaryKeyValue.fromLong(endTime));
        endPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.INF_MAX);
        rangeRowQueryCriteria.setExclusiveEndPrimaryKey(endPrimaryKeyBuilder.build());
    
        rangeRowQueryCriteria.setMaxVersions(1);
    
        String strBaseStationNum = String.format("%d", baseStationNumber);
        String strStartTime = String.format("%d", startTime);
        String strEndTime = String.format("%d", endTime);
    
        System.out.println("The list of calls forwarded by the base station" + strBaseStationNum + "from" + strStartTime + "to" + strEndTime + "is listed as follows:");
        while (true) {
            GetRangeResponse getRangeResponse = client.getRange(new GetRangeRequest(rangeRowQueryCriteria));
            For (Row row: fig. getrows ()){
                PrimaryKey curIndexPrimaryKey = row.getPrimaryKey();
                PrimaryKeyColumn mainCalledNumber = curIndexPrimaryKey.getPrimaryKeyColumn(PRIMARY_KEY_NAME_1);
                PrimaryKeyColumn callStartTime = curIndexPrimaryKey.getPrimaryKeyColumn(PRIMARY_KEY_NAME_2);
                PrimaryKeyBuilder mainTablePKBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
                mainTablePKBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, mainCalledNumber.getValue());
                mainTablePKBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, callStartTime.getValue());
                PrimaryKey mainTablePK = mainTablePKBuilder.build(); // You can specify primary keys for the base table.
    
                // You can query the base table.
                SingleRowQueryCriteria criteria = new SingleRowQueryCriteria(TABLE_NAME, mainTablePK);
                criteria.addColumnsToGet(colName); // You can read the Duration attribute value of the base table.
                // You can specify 1 to indicate the latest data version will be read.
                criteria.setMaxVersions(1);
                GetRowResponse getRowResponse = client.getRow(new GetRowRequest(criteria));
                Row mainTableRow = getRowResponse.getRow();
    
                System.out.println(mainTableRow);
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

    To improve query performance, you can create a compound index on `BaseStationNumber` and `StartTime`. You can specify `Duration` as an attribute of this index.

    The following index is created.

    `IndexOnBaseStation2`:

    |PK0|PK1|PK2|Defined0|
    |:--|:--|:--|:-------|
    |BaseStationNumber|StartTime|CellNumber|Duration|
    |001|1532574644|123456|600|
    |001|1532574714|234567|10|
    |002|1532574795|345678|5|
    |002|1532574861|345678|100|
    |003|1532574734|234567|20|
    |003|1532584054|456789|200|

    You can query the `IndexOnBaseStation2` index:

    ``` {#codeblock_0pf_yr2_cfw}
    private static void getRangeFromIndexTable(SyncClient client,
                                               long baseStationNumber,
                                               long startTime,
                                               long endTime,
                                               String colName) {
        RangeRowQueryCriteria rangeRowQueryCriteria = new RangeRowQueryCriteria(INDEX2_NAME);
    
        // You can specify primary keys.
        PrimaryKeyBuilder startPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        startPrimaryKeyBuilder.addPrimaryKeyColumn(DEFINED_COL_NAME_3, PrimaryKeyValue.fromLong(baseStationNumber));
        startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, PrimaryKeyValue.fromLong(startTime));
        startPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.INF_MIN);
        rangeRowQueryCriteria.setInclusiveStartPrimaryKey(startPrimaryKeyBuilder.build());
    
        // You can specify primary keys.
        PrimaryKeyBuilder endPrimaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        endPrimaryKeyBuilder.addPrimaryKeyColumn(DEFINED_COL_NAME_3, PrimaryKeyValue.fromLong(baseStationNumber));
        endPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_2, PrimaryKeyValue.fromLong(endTime));
        endPrimaryKeyBuilder.addPrimaryKeyColumn(PRIMARY_KEY_NAME_1, PrimaryKeyValue.INF_MAX);
        rangeRowQueryCriteria.setExclusiveEndPrimaryKey(endPrimaryKeyBuilder.build());
    
        // You can specify the attribute name to read.
        rangeRowQueryCriteria.addColumnsToGet(colName);
    
        rangeRowQueryCriteria.setMaxVersions(1);
    
        String strBaseStationNum = String.format("%d", baseStationNumber);
        String strStartTime = String.format("%d", startTime);
        String strEndTime = String.format("%d", endTime);
    
        System.out.println("The duration of calls forwarded by the base station" + strBaseStationNum + "from" + strStartTime + "to" + strEndTime + "is listed as follows:");
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
    ​```
    ```

    Hence, if you do not specify `Duration` as an index attribute, you have to retrieve Duration by querying the base table. However, when you specify `Duration` as an index attribute, this attribute data is stored in the base table and the index. The configuration improves query performance at the cost of disk space consumption.

-   If you want to fetch the following values from a result set: total call duration, the average call duration, the maximum call duration, and the minimum call duration.This result set is a value of BaseStationNumber`003` with a StartTime value range from `1532574861` to `1532584054`.

    Compared to the last query, return is not required for each call duration. However, return is required for duration statistics. You can fetch results using the same method as the last query. Then you can perform Duration calculations to obtain the required result. In addition, you can execute SQL statements in SQL-on-OTS to obtain statistics. For more information about how to activate SQL-on-OTS, see [OLAP on Table Store: serverless SQL big data analysis on Data Lake Analytics](https://yq.aliyun.com/articles/618501?spm=a2c4e.11155435.0.0.701733127JMFla). You can use most MySQL syntax in SQL-on-OTS. Additionally, with SQL-on-OTS, you can easily process complicated calculations that are applicable to your business.


