# Create tables {#concept_pxs_g5k_2gb .concept}

You can call the CreateTable operation to create a table based on specified table schema information.

When creating a table in Table Store, you must specify the primary key of the table. A primary key contains one to four primary key columns. Each primary key column has a name and a data type.

**API**

```
  /**
   * Create the table based on specified table schema information.
   */
  createTable(params, callback)

```

**Note:** After the table is created in Table Store, it takes several seconds to load the table. Do not perform any operations during this period.

**Examples**

The following code provides an example of how to create a table with two primary key columns and a reserved read/write throughput of \(0,0\):

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
    timeToLive: -1,// The data validity period in seconds. –1 indicates that the data never expires. To set the validity period to one year, set timeToLive to 365 × 24 × 3600.
    maxVersions: 1//The maximum number of versions that can be saved in each column. The value of 1 indicates that only the latest version is saved in each column.
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

**Note:** Code details can be obtained at [createTable@GitHub](https://github.com/aliyun/aliyun-tablestore-nodejs-sdk/blob/master/samples/createTable.js).

