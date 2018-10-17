# Error handling {#concept_56416_zh .concept}

## Method {#section_crd_sq2_2fb .section}

The Table Store NodeJS SDK uses the `Exception` method to handle errors. Operations are successful if the called API does not throw an exception. If an exception is thrown, the operation fails.

**Note:** Batch operation APIs such as BatchGetRow and BatchWriteRow are successfully called only when the system checks that the status of each row is successful.

## Exception { .section}

In the Table Store NodeJS SDK, all errors are processed in a unified manner, and are returned to the err parameter of the callback method. Therefore, you must check whether the err parameter has a value before obtaining the returned data. If an error is reported on the Table Store server, RequestId is returned, specifying the UUID that uniquely identifies the request. If the problem persists, save the RequestId and [open a ticket](https://selfservice.console.aliyun.com/ticket/createIndex).

## Retry { .section}

The system automatically retries the operation when an error occurs in the SDK. The default policy sets a maximum of 20 retry attempts and a maximum retry interval of 3000 ms. For more information about how the system retries the operation on throttling errors and read-related internal server errors, see `tablestore/lib/retry.js`.

