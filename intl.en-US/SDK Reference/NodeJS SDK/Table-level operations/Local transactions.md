# Local transactions

After you use a specified partition key value to create a local transaction, you can read and write data within the local transaction. You can commit or abort local transactions.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialization](/intl.en-US/SDK Reference/NodeJS SDK/Initialization.md).
-   A data table is created. Data is written to the table.

## Procedure

To implement a local transaction, perform the following operations:

1.  Use startLocalTransaction to create a local transaction based on the specified partition key value and obtain the ID of the local transaction.
2.  Read and write data within the local transaction.

    You can call the following operations on the local transaction: GetRow, PutRow, DeleteRow, UpdateRow, BatchWriteRow, and GetRange.

3.  Use commitTransaction to commit the local transaction or use abortTransaction to abort the local transaction.

## Parameters

|Parameter|Description|
|---------|-----------|
|tableName|The name of the table.|
|primaryKey|The primary key of the table. -   When you create a local transaction, you need only to specify the partition key value for the local transaction.
-   After a local transaction is created, you must specify a complete primary key when you read data from or write data to the local transaction. |
|transactionId|The ID that identifies a local transaction. After a local transaction is created, the transaction ID must be included for operations on the local transaction. |

## Examples

-   Commit a local transaction for all modified data to take effect.

    ```
    (async () => {
        try {
    
            // Create a local transaction.
            const response = await client.startLocalTransaction({
                tableName,
                primaryKey: [{  // You need only to specify the partition key value for the local transaction.
                    "id": "partitionKeyValue"
                }]
            });
    
            // Obtain the local transaction ID.
            const transactionId = response.transactionId;
    
            // Write data within the local transaction.
            await client.putRow({
                tableName,
                condition: new TableStore.Condition(TableStore.RowExistenceExpectation.IGNORE, null),
                primaryKey,
                attributeColumns: [{
                    col: 'updated'
                }],
                transactionId
            });
    
            // Commit the local transaction.
            await client.commitTransaction({
                transactionId
            })
        } catch (e) {
            console.error(e)
        }
    })();
    ```

-   Abort a local transaction to ignore all modified data

    ```
    (async () => {
        try {
    
            // Create a local transaction.
            const response = await client.startLocalTransaction({
                tableName,
                primaryKey: [{  // You need only to specify the partition key value for the local transaction.
                    "id": "partitionKeyValue"
                }]
            });
            // Obtain the local transaction ID.
            const transactionId = response.transactionId
    
            // Write data within the local transaction.
            await client.putRow({
                tableName,
                condition: new TableStore.Condition(TableStore.RowExistenceExpectation.IGNORE, null),
                primaryKey,
                attributeColumns: [{
                    col: 'updated'
                }],
                transactionId
            });
            // Abort the local transaction.
            await client.abortTransaction({
                transactionId
        })
      } catch (e) {
        console.error(e)
      }
    })();
    ```


