# Query the description information of a table {#concept_iyt_y5k_2gb .concept}

You can call the DescribeTable operation to query the structure information and the reserved read/write throughput of a table.

**API**

```
  /**
   * Query the structure information and the reserved read/write throughput of the specified table.
   */
  describeTable(params, callback) 

```

**Examples**

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

**Note:** Code details can be obtained at [describeTable@GitHub](https://github.com/aliyun/aliyun-tablestore-nodejs-sdk/blob/master/samples/describeTable.js).

