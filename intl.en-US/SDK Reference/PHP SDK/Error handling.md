# Error handling {#concept_31761_zh .concept}

The Table Store PHP SDK adopts the `Exception` method to handle errors. If the called operation does not throw an exception, the operation succeeds. Otherwise, the operation fails.

**Note:** Batch operations such as BatchGetRow and BatchWriteRow can be called only after the system verifies that no exception is thrown and the status of each row is successful.

## Exception { .section}

The Table Store PHP SDK has two types of exceptions: OTSClientException and OTSServerException. Both are inherited from the Exception class.

-   OTSClientException indicates an internal SDK exception, for example, incorrect parameter values.

-   OTSServerException indicates a server error. A server error message provides details about the cause of an exception. OTSServerException has the following components:

    -   getHttpStatus\(\): the returned HTTP status code, for example, 200 or 404.

    -   getOTSErrorCode\(\): the error type string returned by Table Store.

    -   getOTSErrorMessage\(\): the detailed error description returned by Table Store.

    -   getRequestId\(\): the UUID that uniquely identifies a request. If the problem persists, send the request ID to Table Store development engineers for help.


