# BatchWriteRow {#reference1996 .reference}

BatchWriteRow inserts, modifies, or deletes several data rows in one or more tables.

It is essentially a set of multiple PutRow, UpdateRow, and DeleteRow operations. Each operation is executed, returns results independently, and independently consumes capacity units.

Compared to the execution of a large number of write operations, the use of the BatchWriteRow operation can reduce the request response time and increase the data write rate.

## Request structure: {#section_pv6_c78_nhs .section}

``` {#codeblock_3ht_rue_krd .language-pb}
message BatchWriteRowRequest {
    repeated TableInBatchWriteRowRequest tables = 1;
}
			
```

tables:

-   Type: repeated [TableInBatchWriteRowRequest](reseller.en-US/API Reference/Data Types/TableInBatchWriteRowRequest.md#)

-   Required parameter: Yes

-   Specifies the information of rows that require write operations.

-   An error is returned if any of the following conditions occur:

    -   Any table in `tables` does not exist.

    -   `tables` contains tables with the same name.

    -   The name of any table in `tables` does not comply with the [Naming conventions and data types](../../../../reseller.en-US/Developer Guide/Wide column model/Naming conventions and data types.md#).

    -   The primary key is not specified for any row in `tables`, the primary key column name does not comply with the conventions, or the primary key column type is incorrect.

    -   For any attribute column in `tables`, the column name does not comply with the [Naming conventions and data types](../../../../reseller.en-US/Developer Guide/Wide column model/Naming conventions and data types.md#).

    -   Any row in `tables` has an attribute column with the same name as the primary key column.

    -   The value of any primary key or attribute column in `tables` exceeds the [Limits](../../../../reseller.en-US/Developer Guide/Limits.md#).

    -   Any table in `tables` contains rows with identical primary keys.

    -   The total number of rows for all `tables` in tables exceeds 200 or the total size of the data contained exceeds 1M.

    -   If any table in `tables` contains no rows, the OTSParameterInvalidException error is returned.

    -   In `tables`, any PutRowInBatchWriteRowRequest contains more than 1024 columns.

    -   In `tables`, any UpdateRowInBatchWriteRowRequest contains more than 1024 ColumnUpdate objects.


## Response message structure: {#section_oee_r27_r5i .section}

``` {#codeblock_pvf_bcp_jgp .language-pb}
message BatchWriteRowResponse {
    repeated TableInBatchWriteRowResponse tables = 1;
}
			
```

tables:

-   Type: [TableInBatchWriteRowResponse](reseller.en-US/API Reference/Data Types/TableInBatchWriteRowResponse.md#)

-   The response information corresponding to the operations for each table, including whether or not execution was successful, error codes, and the capacity units consumed.

-   The TableInBatchWriteRowResponse object order in the response message is the same as the TableInBatchWriteRowRequest object order in BatchWriteRowRequest. The RowInBatchWriteRowResponse object orders contained in put\_rows, update\_rows, and delete\_rows in each TableInBatchWriteRowRequest are the same as the respective PutRowInBatchWriteRowRequest, UpdateRowInBatchWriteRowRequest, and DeleteRowInBatchWriteRowRequest object orders contained in put\_rows, update\_rows, and delete\_rows in TableInBatchWriteRowRequest.

-   If a row fails to be read, the row’s is\_ok value in RowInBatchWriteRowResponse is false.


**Note:** At the row level, the BatchWriteRow operation may partially fail. In this case, it still returns an HTTP status code of 200, but the application must check errors in RowInBatchWriteRowResponse to confirm the execution results for each row and then proceed accordingly.

Capacity unit consumption:

-   If the entire operation fails, no capacity units are consumed.

-   If request time-out occurs and the results are undefined, a capacity unit may or may not be consumed.

-   In other situations, each PutRowInBatchWriteRowRequest, UpdateRowInBatchWriteRowRequestDelete, and RowInBatchWriteRowRequest operation corresponds to a separate write operation when the number of write capacity units are counted. For more information, see [PutRow](reseller.en-US/API Reference/Operations/PutRow.md#), [UpdateRow](reseller.en-US/API Reference/Operations/UpdateRow.md#) and [DeleteRow](reseller.en-US/API Reference/Operations/DeleteRow.md#).


