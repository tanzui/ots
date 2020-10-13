# Configure an auto-increment primary key column

## Prerequisites

The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/NodeJS SDK/Initialization.md).

## Usage notes

1.  When you write data to a table, you do not need to specify a specific value for the auto-increment column. Instead, you need only to set the value of the auto-increment column to placeholders.

    If you want to obtain the value of the auto-increment column after data is written to the table, you can set ReturnType to Primarykey.

    When you query data, you must specify the values of all primary key columns. To obtain a complete primary key value, you can set ReturnType to Primarykey in PutRow, UpdateRow, or BatchWriteRow.


## Examples

When you use auto-increment primary key columns, you can call the CreateTable, PutRow, UpdateRow, or BatchWriteRow operation.

1.  Create a table

    To create an auto-increment primary key column when you create a table, you must set the attribute of the primary key column to AUTO\_INCREMENT.

    ```
    var TableStore = require('../index.js');
    var Long = TableStore.Long;
    var client = require('./client');
    
    var tableName = "autoIncTable";
    var pk1 = "stringPK";
    var pk2 = "autoIncPK";
    
    function createTableWithAutoIncrementPk() {
        var createParams = {
            tableMeta: {
                tableName: tableName,
                primaryKey: [
                    {
                        name: pk1,
                        type: 'STRING'
                    },
                    {
                        name: pk2,
                        type: 'INTEGER',
                        option: 'AUTO_INCREMENT'// Set the auto-increment column by setting option to AUTO_INCREMENT.
                    },
                ]
            },
            reservedThroughput: {
                capacityUnit: {
                    read: 0,
                    write: 0
                }
            },
            tableOptions: {
                timeToLive: -1,// Specify the validity period of data in seconds. A value of -1 indicates that the data never expires. If you set the validity period to a year, the value of timeToLive is 365*24*3600.
                maxVersions: 1// Specify the maximum number of versions that can be saved in each column. A value of 1 indicates that only the latest version is saved in each column.
            },
        };
    
        client.createTable(createParams, function (err, data) {
            if (err) {
                console.log('error:', err);
                return;
            }
            console.log('create table success');
        });
    }
    ```

2.  Write data

    When you write data to a table, you do not need to specify a specific value of the auto-increment column. Instead, you need only to set the value of the auto-increment column to AUTO\_INCREMENT.

    ```
    var TableStore = require('../index.js');
    var Long = TableStore.Long;
    var client = require('./client');
    
    var tableName = "autoIncTable";
    var pk1 = "stringPK";
    var pk2 = "autoIncPK";
    
    function putRow() {
        var putParams = {
            tableName: tableName,
            condition: new TableStore.Condition(TableStore.RowExistenceExpectation.IGNORE, null),
            primaryKey: [
                { stringPK: 'pk1' },
                { autoIncPK: TableStore.PK_AUTO_INCR }
            ],
            attributeColumns: [
                { 'col1': 'col1val' }
            ],
            returnContent: { returnType: TableStore.ReturnType.Primarykey }
        };
    
        client.putRow(putParams, function (err, data) {
            if (err) {
                console.log('error:', err);
                return;
            }
    
            console.log('put row success,autoIncrement pk value:' + JSON.stringify(data.row.primaryKey));
        });
    
    }
    ```


