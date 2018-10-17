# Error handling {#concept_31769_zh .concept}

TableStore C++ SDK uses return value to handle errors. All interfaces that can generate errors return `Optional<OTSError>` objects.

-    `Optional<T>` is a template class defined in tablestore/util/optional.hpp. Think of it as a box that can store one `T` object at most. Inside the box are two scenarios that can be used to judge whether an error has occurred:

    -   If a `T` object exists, it can be taken out and used. In this case, `Optional<T>::present()` returns true, indicating that an error has occurred.

    -   If no `T` object exists, `Optional<T>::present()` returns false, indicating that no error occurred.

-   The OTSError object indicates a specific error. It has 5 fields:

    -    `httpStatus` and `errorCode`: HTTP return code and error code. In addition to the , the following errors only happen on the client.

        |6|OTSCouldntResolveHost|Unable to resolve the domain name. The instance access address is wrong or the network is disconnected.|
        |7|OTSCouldntConnect|Unable to connect to the service. Configuration error in local host files.|
        |28|OTSRequestTimeout|Request timeout.|
        |35|OTSSslHandshakeFail|HTTPS handshake failure. Local certificate not installed.|
        |55|OTSWriteRequestFail|Network delivery failure. Network interruption.|
        |56|OTSCorruptedResponse|Incomplete response.|
        |89|OTSNoAvailableConnection|No available connection. This usually happens in newly constructed clients or when concurrent requests exceed the total number of network connections.|

    -   `message`: Error details.
    -   `requestId`: Each request sent to the service is assigned a number by the service. If a response is returned normally, the response object contains the requestId. If the service determines that a request has errors, the error object contains the requestId. If errors occur before the request is sent, or if they happen on the network link, the error object does not contain the requestId.
    -   `traceId`: Each API call is assigned a traceId by the client. Different API calls are assigned a different traceId. If the same API call is tried repeatedly, the traceId remains the same but the requestId may vary.

