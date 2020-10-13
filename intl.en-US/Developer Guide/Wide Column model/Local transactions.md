# Local transactions

This topic describes how to use local transactions. You can create a local transaction based on a specified partition key value. After you read or write data within a local transaction, you can commit or abort the local transaction.

The local transaction feature is available for invitational preview and is disabled by default. To use local transactions, [submit a ticket](https://workorder-intl.console.aliyun.com/#/ticket/createInd) to apply for a trail.

The local transaction feature allows you to perform atomic operations to read or write a single row or multiple rows.

## Scenarios

-   Read and write operations \(simple scenarios\)

    You can use the following methods to perform read, modify, and write \(RMW\) operations. Different methods have different limits.

    -   Conditional update: You can perform conditional update on only a single row in a single request. You cannot perform conditional update on multiple rows or for multiple write operations. For more information, see [Conditional update](/intl.en-US/Developer Guide/Wide Column model/Conditional update.md).
    -   Atomic counters: You can configure an atomic counter only for a single row in a single request. You can use atomic counters only to increment the values in columns. For more information, see [Atomic counters](/intl.en-US/Developer Guide/Wide Column model/Atomic counters.md).
    You can create a local transaction to perform an RMW operation on data within the range specified based on a partition key value.

    1.  Use StartLocalTransaction to create a local transaction based on a specified partition key value and obtain the ID of the local transaction.
    2.  Use GetRow or GetRange to read data. The transaction ID must be included in the request.
    3.  Modify data on the client.
    4.  Use PutRow, UpdateRow, DeleteRow, or BatchWriteRow to write back the modified data. The transaction ID must be included in the request.
    5.  Use CommitTransaction to commit the transaction.
-   Email \(complex scenarios\)

    You can create a local transaction to perform atomic operations on emails of the same user.

    To use the local transaction feature, include a base table and two index tables in a physical table. The following table provides the primary key columns.

    The Type column is used to specify rows of the base table and rows of index tables. The IndexField column stores the folder names for the Folder index table, and the send times for the SendTime index table. The IndexField column is empty for the base table.

    |Table|UserID|Type|IndexField|MailID|
    |:----|:-----|:---|:---------|:-----|
    |Base table|User ID|"Main"|"N/A"|Email ID|
    |Folder table|User ID|"Folder"|$Folder|Email ID|
    |SendTime table|User ID|"SendTime"|$SendTime|Email ID|

    You can use a local transaction to perform the following operations:

    -   List the latest 100 emails that a user has sent by taking the following steps:
        1.  Use the user ID to create a local transaction and obtain the transaction ID.
        2.  Use the transaction ID to call GetRange to query 100 emails from the SendTime table.
        3.  Use the transaction ID to call BatchGetRow to query the detailed information of the 100 emails from the base table.
        4.  Use CommitTransaction to commit the local transaction or use AbortTransaction to abort the local transaction.

            The commit and abort operations have the same effect because no writes are performed in this transaction.

    -   Transfer all emails in a folder to another folder by performing the following steps:
        1.  Use the user ID to create a local transaction and obtain the transaction ID.
        2.  Use the transaction ID to call GetRange to query multiple emails from the Folder table.
        3.  Use the transaction ID to call BatchWriteRow to perform write operations on the Folder table.

            The write operation is performed on two rows each time when an email is transferred. Specifically, a row is deleted from the old Folder table and a row is added to the new Folder table.

        4.  Use CommitTransaction to commit the transaction.
    -   Count the numbers of read emails and unread emails in a folder by taking the following steps \(not the optimal solution\):
        1.  Use the user ID to create a local transaction and obtain the transaction ID.
        2.  Use the transaction ID to call GetRange to query multiple emails from the Folder table.
        3.  Use the transaction ID to call BatchGetRow to query the read status of each email from the base table.
        4.  Use CommitTransaction to commit the local transaction or use AbortTransaction to abort the local transaction.

            The commit and abort operations have the same effect because no writes are performed in this transaction.

    In this scenario, you can add more index tables to accelerate common operations. The local transaction feature solves the status inconsistency issue between the base table and index tables and simplifies development. For example, when you count the number of emails, many emails are read, which results in a large overhead. To reduce overhead and accelerate query, use a new index table to store the numbers of read emails and unread emails.


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

## Operations

The following table describes the operations that can be used to perform operations on local transactions.

|Operation|Description|
|---------|-----------|
|StartLocalTransaction|Creates a local transaction.|
|CommitTransaction|Commits a local transaction.|
|AbortTransaction|Aborts a local transaction.|
|GetRow|Reads and writes data within a local transaction. For more information, see [Data operations](/intl.en-US/Developer Guide/Wide Column model/Data operations.md).|
|PutRow|
|UpdateRow|
|DeleteRow|
|BatchWriteRow|
|GetRange|

## Usage

You can use the following SDKs to implement local transactions:

-   SDK for Java: [Local transactions](/intl.en-US/SDK Reference/Java SDK/Table/Local transactions.md)
-   SDK for Go: [Local transactions](/intl.en-US/SDK Reference/Go SDK/Table-level operations/Local transactions.md)
-   SDK for Python: [Local transactions](/intl.en-US/SDK Reference/Python SDK/Table-level operations/Local transactions.md)
-   SDK for Node.js: [Local transactions](/intl.en-US/SDK Reference/NodeJS SDK/Table-level operations/Local transactions.md)
-   SDK for PHP: [Local transactions](/intl.en-US/SDK Reference/PHP SDK/Table operations/Local transactions.md)

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


## Billing methods

-   Each of the StartLocalTransaction, CommitTransaction, and AbortTransaction operations consumes one write capacity unit \(CU\).
-   The read and write requests on local transactions are billed in the same way as regular read and write requests. For more information, see [Billing overview](/intl.en-US/Pricing/Billing overview.md).

## Error codes

|Error code|Error message|
|----------|-------------|
|OTSRowOperationConflict|The error message returned because the specified partition key value is already used by an existing local transaction.|
|OTSSessionNotExist|The error message returned because the transaction that has the specified transaction ID does not exist, or the specified transaction is invalid or times out.|
|OTSSessionBusy|The error message returned because the last request on the transaction is incomplete.|
|OTSOutOfTransactionDataSizeLimit|The error message returned because the volume of data in a transaction exceeds the maximum volume.|
|OTSDataOutOfRange|The error message returned because data is operated beyond the range specified by the partition key value used to create the transaction.|

