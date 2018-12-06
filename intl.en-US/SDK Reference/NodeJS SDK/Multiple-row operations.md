# Multiple-row operations {#reference4298 .reference}

The Table Store SDK provides the following multi-row operation APIs: BatchGetRow, BatchWriteRow, GetRange, and GetByIterator.

## BatchGetRow { .section}

Reads several data rows in batches from one or more tables.

The BatchGetRow operation is essentially a set of multiple GetRow operations. Each operation is executed, results are returned, and capacity units are consumed independently.

Compared to the execution of a large number of GetRow operations, the BatchGetRow operation effectively reduces the request response time and increases the data read rate.

**API**

```
  /**
   * Read several data rows in batches from one or more tables.
   */
  batchGetRow(params, callback)

```

**Example**

Read multiple tables and multiple data rows in batches, and retry the operation if an error occurs in a single row.

```
var client = require('./client');
var TableStore = require('../index.js');
var Long = TableStore.Long;

var params = {
    tables: [{
        tableName: 'sampleTable',
        primaryKey: [
            [{ 'gid': Long.fromNumber(20013) }, { 'uid': Long.fromNumber(20013) }],
            [{ 'gid': Long.fromNumber(20015) }, { 'uid': Long.fromNumber(20015) }]
        ],
        startColumn: "col2",
        endColumn: "col4"
    },
    {
        tableName: 'notExistTable',
        primaryKey: [
            [{ 'gid': Long.fromNumber(10001) }, { 'uid': Long.fromNumber(10001) }]
        ]
    }
    ],
};

var maxRetryTimes = 3;
var retryCount = 0;

function batchGetRow(params) {
    client.batchGetRow(params, function (err, data) {
        if (err) {
            console.log('error:', err);
            return;
        }

        var isAllSuccess = true;
        var retryRequest = { tables: [] };
        for (var i = 0; i < data.tables.length; i++) {
            var faildRequest = { tableName: data.tables[i][0].tableName, primaryKey: [] };

            for (var j = 0; j < data.tables[i].length; j++) {
                if (! data.tables[i][j].isOk && null ! = data.tables[i][j].primaryKey) {
                    isAllSuccess = false;
                    var pks = [];
                    for (var k in data.tables[i][j].primaryKey) {
                        var name = data.tables[i][j].primaryKey[k].name;
                        var value = data.tables[i][j].primaryKey[k].value;
                        var kp = {};
                        kp[name] = value;
                        pks.push(kp);
                    }
                    faildRequest.primaryKey.push(pks);

                } else {
                    // get success data
                }
            }

            if (faildRequest.primaryKey.length > 0) {
                retryRequest.tables.push(faildRequest);
            }
        }

        if (! isAllSuccess && retryCount++ < maxRetryTimes) {
            batchGetRow(retryRequest);
        }

        console.log('success:', data);
    });
}

batchGetRow(params, maxRetryTimes);


```

**Note:** 

-   BatchGetRow supports filtering using conditional statements.
-   Obtain the full sample codes at [BatchGetRow@GitHub](https://github.com/aliyun/aliyun-tablestore-nodejs-sdk/blob/master/samples/batchGetRow.js).

## BatchWriteRow { .section}

Inserts, modifies, or deletes several data rows in batches in one or more tables.

The BatchWriteRow operation is essentially a set of multiple PutRow, UpdateRow, and DeleteRow operations. Each operation is executed, results are returned, and capacity units are consumed independently.

**API**

```
  /**
   * Modify data rows in batches.
   */
  batchWriteRow(params, callback)

```

**Example**

Write data rows in batches.

```
var client = require('./client');
var TableStore = require('../index.js');
var Long = TableStore.Long;

var params = {
    tables: [{
        tableName: 'sampleTable',
        rows: [{
            type: 'PUT',
            condition: new TableStore.Condition(TableStore.RowExistenceExpectation.IGNORE, null),
            primaryKey: [{ 'gid': Long.fromNumber(8) }, { 'uid': Long.fromNumber(80) }],
            attributeColumns: [{ 'attrCol1': 'test1' }, { 'attrCol2': 'test2' }],
            returnContent: { returnType: TableStore.ReturnType.Primarykey }
        }],
    }],
};

client.batchWriteRow(params, function (err, data) {
    
    if (err) {
        console.log('error:', err);
        return;
    }

    console.log('success:', data);
});


```

**Note:** 

-   BatchWriteRow supports filtering using conditional statements.
-   Obtain the full sample codes at [BatchWriteRow@GitHub](https://github.com/aliyun/aliyun-tablestore-nodejs-sdk/blob/master/samples/batchWriteRow.js).

## GetRange { .section}

Reads data within the specified primary key range.

**API**

```
  /**
   * Read data within the specified primary key range.
   */
  getRange(params, callback)

```

**Example**

Read data within the specified range.

```
var Long = TableStore.Long;
var client = require('./client');

var params = {
  tableName: "sampleTable",
  direction: TableStore.Direction.FORWARD,
  inclusiveStartPrimaryKey: [{ "gid": TableStore.INF_MIN }, { "uid": TableStore.INF_MIN }],
  exclusiveEndPrimaryKey: [{ "gid": TableStore.INF_MAX }, { "uid": TableStore.INF_MAX }],
  limit: 50
};

client.getRange(params, function (err, data) {
  if (err) {
    console.log('error:', err);
    return;
  }

  //If the value of data.next_start_primary_key is not empty, continue to read the data
  if (data.next_start_primary_key) {

  }

  console.log('success:', data);
});


```

**Note:** 

-   GetRange supports filtering using conditional statements.
-   When performing the GetRange operation, note that the data may be paged.
-   Obtain the full sample codes at [GetRange@GitHub](https://github.com/aliyun/aliyun-tablestore-nodejs-sdk/blob/master/samples/getRange.js).

