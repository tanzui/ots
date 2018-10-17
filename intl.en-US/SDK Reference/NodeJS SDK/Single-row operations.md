# Single-row operations {#concept_56354_zh .concept}

The Table Store SDK provides the following single-row operation APIs: PutRow, GetRow, UpdateRow, and DeleteRow.

## PutRow {#section_mpx_s42_2fb .section}

Inserts data into the specified row.

**API**

```
  /**
   * Insert data in the specified row. If this row does not exist, a new row is added. If the row exists, the original row is overwritten.
   */
  putRow(params, callback) 

```

**Example**

```
var TableStore = require('../index.js');
var Long = TableStore.Long;
var client = require('./client');

var currentTimeStamp = Date.now();
var params = {
  tableName: "sampleTable",
  condition: new TableStore.Condition(TableStore.RowExistenceExpectation.IGNORE, null),
  primaryKey: [{ 'gid': Long.fromNumber(20013) }, { 'uid': Long.fromNumber(20013) }],
  attributeColumns: [
    { 'col1': 'Table Store' },
    { 'col2': '2', 'timestamp': currentTimeStamp },
    { 'col3': 3.1 },
    { 'col4': -0.32 },
    { 'col5': Long.fromNumber(123456789) }
  ],
  returnContent: { returnType: TableStore.ReturnType.Primarykey }
};

client.putRow(params, function (err, data) {
  if (err) {
    console.log('error:', err);
    return;
  }

  console.log('success:', data);
});

```

-   RowExistenceExpectation.IGNORE indicates that new data is still inserted no matter whether the specified row exists or not. If the inserted data is the same as the existing data, the existing data is overwritten.

-   RowExistenceExpectation.EXPECT\_EXIST indicates that new data is inserted only when the specified row exists. The existing data is overwritten.

-   RowExistenceExpectation.EXPECT\_NOT\_EXIST indicates that data is inserted only when the specified row does not exist.


**Note:** Obtain the full sample codes at [PutRow@GitHub](https://github.com/aliyun/aliyun-tablestore-nodejs-sdk/blob/master/samples/putRow.js).

## GetRow { .section}

This API reads a single data row based on a given primary key.

**API**

```
  /**
   * Read a single data row based on a given primary key.
   */
  getRow(params, callback)

```

**Example**

Read a data row.

```
var TableStore = require('../index.js');
var Long = TableStore.Long;
var client = require('./client');

var params = {
  tableName: "sampleTable",
  primaryKey: [{ 'gid': Long.fromNumber(20004) }, { 'uid': Long.fromNumber(20004) }],
  maxVersions: 2
};
var condition = new TableStore.CompositeCondition(TableStore.LogicalOperator.AND);
condition.addSubCondition(new TableStore.SingleColumnCondition('name', 'john', TableStore.ComparatorType.EQUAL));
condition.addSubCondition(new TableStore.SingleColumnCondition('addr', 'china', TableStore.ComparatorType.EQUAL));

params.columnFilter = condition;

client.getRow(params, function (err, data) {
  if (err) {
    console.log('error:', err);
    return;
  }
  console.log('success:', data);
});

```

**Note:** Obtain the full sample codes at [GetRow@GitHub](https://github.com/aliyun/aliyun-tablestore-nodejs-sdk/blob/master/samples/getRow.js).

## UpdateRow { .section}

Updates the data of the specified row. If the row does not exist, a new row is added. If the row exists, the values of the specified columns are added, modified, or deleted based on the request content.

**API**

```
  /**
   * Update the data of the specified row. If the row does not exist, a new row is added. If the row exists, the values of the specified columns are added, modified, or deleted based on the request content.
   */
  updateRow(params, callback)

```

**Example**

Update a data row.

```
var TableStore = require('../index.js');
var Long = TableStore.Long;
var client = require('./client');

var params = {
    tableName: "sampleTable",
    condition: new TableStore.Condition(TableStore.RowExistenceExpectation.IGNORE, null),
    primaryKey: [{ 'gid': Long.fromNumber(9) }, { 'uid': Long.fromNumber(90) }],
    updateOfAttributeColumns: [
        { 'PUT': [{ 'col4': Long.fromNumber(4) }, { 'col5': '5' }, { 'col6': Long.fromNumber(6) }] },
        { 'DELETE': [{ 'col1': Long.fromNumber(1496826473186) }] },
        { 'DELETE_ALL': ['col2'] }
    ]
};

client.updateRow(params,
    function (err, data) {
        if (err) {
            console.log('error:', err);
            return;
        }

        console.log('success:', data);
    });

```

**Note:** Obtain the full sample codes at [UpdateRow@GitHub](https://github.com/aliyun/aliyun-tablestore-nodejs-sdk/blob/master/samples/updateRow.js).

## DeleteRow { .section}

**API**

```
  /**
   * Delete a data row.
   */
  deleteRow(params, callback) 

```

**Example**

Delete a data row.

```
var TableStore = require('../index.js');
var Long = TableStore.Long;
var client = require('./client');

var params = {
    tableName: "sampleTable",
    condition: new TableStore.Condition(TableStore.RowExistenceExpectation.IGNORE, null),
    primaryKey: [{ 'gid': Long.fromNumber(8) }, { 'uid': Long.fromNumber(80) }]
};

client.deleteRow(params, function (err, data) {
    if (err) {
        console.log('error:', err);
        return;
    }

    console.log('success:', data);
});


```

**Note:** Obtain the full sample codes at [DeleteRow@GitHub](https://github.com/aliyun/aliyun-tablestore-nodejs-sdk/blob/master/samples/deleteRow.js).

