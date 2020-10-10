# Troubleshooting

This topic describes the troubleshooting methods in Tablestore SDK for Java.

## Methods

Tablestore SDK for Java handles errors as exceptions. The operation is successful if no exceptions are returned for the called operation. If an exception is returned, the operation fails.

**Note:** Batch operations such as BatchGetRow and BatchWriteRow can be called only after the system verifies that no exception is returned and that the status of each row is successful.

## Exceptions

ClientException and TableStoreException are common exceptions you may encounter when you use Tablestore SDK for Java. Both are inherited from RuntimeException.

-   ClientException: an internal SDK exception such as an invalid parameter value.

-   TableStoreException: a server error parsed from a server error message. TableStoreException provides you with the following information:

    -   getHttpStatus\(\): an HTTP status code such as 200 or 404.

    -   getErrorCode\(\): an error type string returned by Tablestore.

    -   getRequestId\(\): the UUID that identifies a request. If a problem persists, record this request ID and[submit a ticket](https://workorder-intl.console.aliyun.com/#/ticket/createInd).


