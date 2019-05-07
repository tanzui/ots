# Delete tables {#concept_tqm_bvk_2gb .concept}

You can call the DeleteTable operation to delete a table from a specified instance.

**API**

```
  /**
   * Delete a table from a specified instance.
   */
  deleteTable(params, callback)

```

**Examples**

The following code provides an example of how to delete a table from a specified instance:

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

**Note:** Code details can be obtained at [deleteTable@GitHub](https://github.com/aliyun/aliyun-tablestore-nodejs-sdk/blob/master/samples/deleteTable.js).

