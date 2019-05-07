# List table names {#concept_sqx_q5k_2gb .concept}

You can call the ListTable operation to obtain the names of all tables that are created in the current instance.

**API**

```
  /**
   * Obtain the names of all tables in the current instance.
   */
  listTable(params, callback)

```

**Examples**

The following code provides an example of how to obtain the names of all tables in the current instance:

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

**Note:** Code details can be obtained at [listTable@GitHub](https://github.com/aliyun/aliyun-tablestore-nodejs-sdk/blob/master/samples/listTable.js).

