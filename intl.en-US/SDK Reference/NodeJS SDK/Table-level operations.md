# Table-level operations {#concept_56353_zh .concept}

The Table Store SDK provides the following APIs for operating tables: CreateTable, ListTable, DeleteTable, UpdateTable, and DescribeTable.

## CreateTable {#section_cfn_5n2_2fb .section}

Creates a table based on the given table structure information.

When creating a table in Table Store, you must specify the table’s primary keys. A primary key contains one to four primary key columns. Each primary key column has a name and a type.

**API**

```
  /**
   * Create a table based on the given table structure information.
   */
  createTable(params, callback)

```

**Note:** After a table is created in Table Store, it takes several seconds to load the table. Do not perform any operations during this time.

**Example**

Create a table with two primary key columns and a reserved read/write throughput of \(0,0\).

```
var client = require('./client');

var params = {
  tableMeta: {
    tableName: 'sampleTable',
    primaryKey: [
      {
        name: 'gid',
        type: 'INTEGER'
      },
      {
        name: 'uid',
        type: 'INTEGER'
      }
    ]
  },
  reservedThroughput: {
    capacityUnit: {
      read: 0,
      write: 0
    }
  },
  tableOptions: {
    timeToLive: -1,// The data expiration time in seconds; –1 indicates that the data does not expire. To set the expiration time to one year, use 365 * 24 * 3600
    maxVersions: 1//The max number of versions to save. If it is set to 1, each column only saves the latest version
  }
};

client.createTable(params, function (err, data) {
  if (err) {
    console.log('error:', err);
    return;
  }
  console.log('success:', data);
});

```

**Note:** Obtain the full sample codes at [createTable@GitHub](https://github.com/aliyun/aliyun-tablestore-nodejs-sdk/blob/master/samples/createTable.js).

## ListTable { .section}

Used to get the names of all tables created in the current instance.

**API**

```
  /**
   * Obtain the names of all tables under the current instance.
   */
  listTable(params, callback)

```

**Example**

Obtain the names of all tables under an instance.

```
var client = require('./client');

client.listTable({}, function (err, data) {
    if (err) {
        console.log('error:', err);
        return;
    }
    console.log('success:', data);
});


```

**Note:** Obtain the full sample codes at [listTable@GitHub](https://github.com/aliyun/aliyun-tablestore-nodejs-sdk/blob/master/samples/listTable.js).

## UpdateTable { .section}

Updates maxVersions and the reserved read or write throughput value of the specified table.

**API**

```
  /**
   * Update the reserved read or write throughput value of the specified table.
   */
  updateTable(params, callback) 

```

**Example**

Set maxVersions of a table to 5.

```
var client = require('./client');

var params = {
    tableName: 'sampleTable',
    tableOptions: {
        maxVersions: 5,
    }
};

client.updateTable(params, function (err, data) {
    if (err) {
        console.log('error:', err);
        return;
    }
    console.log('success:', data);
});


```

**Note:** Obtain the full sample codes at [updateTable@GitHub](https://github.com/aliyun/aliyun-tablestore-nodejs-sdk/blob/master/samples/updateTable.js).

## DescribeTable { .section}

Queries the structure information and the reserved read/write throughput value of the specified table.

**API**

```
  /**
   * Query the structure information and the reserved read/write throughput value of the specified table.
   */
  describeTable(params, callback) 

```

**Example**

```
var client = require('./client');

var params = {
    tableName: 'sampleTable'
};

client.describeTable(params, function (err, data) {
    if (err) {
        console.log('error:', err);
        return;
    }
    console.log('success:', data);
});


```

**Note:** Obtain the full sample codes at [describeTable@GitHub](https://github.com/aliyun/aliyun-tablestore-nodejs-sdk/blob/master/samples/describeTable.js).

## DeleteTable { .section}

Deletes the specified table under an instance.

**API**

```
  /**
   * Delete the specified table under an instance.
   */
  deleteTable(params, callback)

```

**Example**

Delete a table.

```
var client = require('./client');

var params = {
    tableName: 'sampleTable'
};

client.deleteTable(params, function (err, data) {
    if (err) {
        console.log('error:', err);
        return;
    }
    console.log('success:', data);
});

```

**Note:** Obtain the full sample codes at [deleteTable@GitHub](https://github.com/aliyun/aliyun-tablestore-nodejs-sdk/blob/master/samples/deleteTable.js).

