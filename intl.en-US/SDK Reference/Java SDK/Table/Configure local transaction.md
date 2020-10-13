# Configure local transaction

The local transaction feature is available for invitational preview and is disabled by default. To use local transactions, [submit a ticket](https://workorder-intl.console.aliyun.com/#/ticket/createInd) to apply for a trail.

You can use local transaction to specify that the operations on a partition key are atomic, which indicates that operations based on the specified partition key either succeed or fail. The isolation level of the local transaction is serializable.

## Prerequisites

-   The OTSClient instance is initialized. For more information, see [Initialize SDK for Java](/intl.en-US/SDK Reference/Java SDK/Initialize SDK for Java.md).
-   A data table is created. Data is written to the table.

## Usage notes

1.  Use StartLocalTransaction to create a local transaction based on the specified partition key value and obtain the ID of the local transaction.
2.  Read and write data within the local transaction.

    You can call the following operations on the local transaction: GetRow, PutRow, DeleteRow, UpdateRow, BatchWriteRow, and GetRange.

3.  Use CommitTransaction to commit local transactions or use AbortTransaction to abort local transactions.

## Limits

-   The validity period of a local transaction is up to 60 seconds.

    If a transaction is not committed or aborted within 60 seconds, the server of Tablestore determines that the transaction times out and aborts it.

-   A transaction may be created in the server side of Tablestore even if a timeout error is returned. In this case, you can resend a transaction creation request only after the created transaction times out.
-   If an uncommitted transaction becomes invalid, retry the operation within this transaction.
-   Tablestore imposes the following limits on read and write operations on data within a local transaction:
    -   The transaction ID cannot be used to access data beyond the range specified based on the partition key value that is used to create the transaction.
    -   The partition key values of all write requests in the same transaction must be the same as the partition key value used to create the transaction. This limit does not apply to read requests.
    -   A local transaction can be used by only one request at a time. When the transaction is in use, other operations that use the transaction ID fail.
    -   The maximum interval for read and write operations on a transaction is 60 seconds.

        If a transaction is not read or written for more than 60 seconds, the server of Tablestore determines that the transaction times out and aborts it.

    -   Up to 4 MB of data can be written to each transaction. The volume of written data to each transaction is calculated in the same way as a regular write request.
    -   If you do not specify a version for a cell, the server of Tablestore assigns a version to the cell in the usual way when the cell is written into the transaction \(rather than when the transaction is committed\).
    -   If a BatchWriteRow request includes a transaction ID, all rows in the request can only be written to the table that matches the transaction ID.
    -   When you use a transaction, the data within the range specified based on the corresponding partition key value is locked. Write requests on data within the transaction but without the transaction ID are rejected. The data in the transaction is unlocked when the transaction is committed or aborted, or when the transaction times out.
    -   A transaction remains valid even if a read or write request with the transaction ID is rejected. You can resend the request in the same way as a regular request or abort the transaction.

## Examples

1.  Call the startLocalTransaction method of AsyncClient or SyncClient to create a local transaction based on a specified partition key value and obtain the ID of the local transaction.

    ```
    PrimaryKeyBuilder primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
    primaryKeyBuilder.addPrimaryKeyColumn("pk1", PrimaryKeyValue.fromString("txnKey"));
    PrimaryKey primaryKey = primaryKeyBuilder.build();
    StartLocalTransactionRequest request = new StartLocalTransactionRequest(tableName, primaryKey);
    String txnId = client.startLocalTransaction(request).getTransactionID();
    ```

2.  Read and write data within the local transaction.

    You must specify the transaction ID to read and write data within the local transaction. The read and write operations are similar to the regular operations.

    **Note:** The range of the current local transaction is within a partition key value. For more information about partition keys, see [Primary keys and attributes](/intl.en-US/Developer Guide/Wide Column model/Primary keys and attributes.md).

    -   Write a row to the local transaction.

        ```
        PrimaryKeyBuilder primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        primaryKeyBuilder.addPrimaryKeyColumn("pk1", PrimaryKeyValue.fromString("txnKey"));
        primaryKeyBuilder.addPrimaryKeyColumn("pk2", PrimaryKeyValue.fromLong("userId"));
        PrimaryKey primaryKey = primaryKeyBuilder.build();
        RowPutChange rowPutChange = new RowPutChange(tableName, primaryKey);
        rowPutChange.addColumn(new Column("Col", ColumnValue.fromLong(columnValue)));
        
        PutRowRequest request = new PutRowRequest(rowPutChange);
        request.setTransactionId(txnId);
        client.putRow(request);
        ```

    -   Read the row from the local transaction.

        ```
        PrimaryKeyBuilder primaryKeyBuilder;
        primaryKeyBuilder = PrimaryKeyBuilder.createPrimaryKeyBuilder();
        primaryKeyBuilder.addPrimaryKeyColumn("pk1", PrimaryKeyValue.fromString("txnKey"));
        primaryKeyBuilder.addPrimaryKeyColumn("pk2", PrimaryKeyValue.fromLong("userId"));
        PrimaryKey primaryKey = primaryKeyBuilder.build();
        SingleRowQueryCriteria criteria = new SingleRowQueryCriteria(tableName, primaryKey);
        criteria.setMaxVersions(1); // Specify to read the latest version of data.
        
        GetRowRequest  request = new GetRowRequest(criteria);
        request.setTransactionId(txnId);
        GetRowResponse getRowResponse = client.getRow(request);
        ```

3.  Commit or abort the local transaction.
    -   Commit the local transaction for all data modification to take effect.

        ```
        CommitTransactionRequest commitRequest = new CommitTransactionRequest(txnId);
        client.commitTransaction(commitRequest);
        ```

    -   Abort the local transaction so that all data modification does not take effect.

        ```
        AbortTransactionRequest abortRequest = new AbortTransactionRequest(txnId);
        client.abortTransaction(abortRequest);
        ```


