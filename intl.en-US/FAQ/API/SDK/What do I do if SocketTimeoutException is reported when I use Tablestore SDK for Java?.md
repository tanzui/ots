# What do I do if SocketTimeoutException is reported when I use Tablestore SDK for Java?

## Problem description

When the difference between the time Tablestore SDK for Java receives data and the time Tablestore SDK for Java sends data exceeds the socket timeout value, Tablestore SDK for Java returns SocketTimeoutException. The socket timeout period includes the period for network transmission between the application and server, which is calculated from the time the application sends a request to when server processes the request and when the application receives the response. You can customize the socket timeout value when you create an OTSClient instance. By default, the socket timeout value is 15s.

## Solution

Solutions are provided based on the following possible causes:

-   Network connection failure

    If SocketTimeoutException is returned for all requests, the possible cause is network connection failures. You can run the ping or curl command to test the connectivity of the network.

    ```
    ping aaaa.cn-hangzhou.ots.aliyuncs.com
    curl aaaa.cn-hangzhou.ots.aliyuncs.com
    					
    ```

    In most cases, the curl command returns a similar output:

    ```
    <? xml version="1.0" encoding="UTF-8"? >
    <Error><Code>OTSUnsupportOperation</Code><Message>Unsupported operation: ''. </Message><RequestID>00054ec5-822c-8964-adaf-990a07a4d0c9</RequestID><HostID>MTAuMTUzLjE3NS4xNzM=</HostID></Error>
    					
    ```

    If the connection fails, a possible cause is that an internal endpoint is used when the request is not sent by an ECS instance.

-   Server-side latency that exceeds the socket timeout value specified in Tablestore SDK for Java

    The period for the Tablestore server to process a request does not exceed 15 seconds because the default maximum overtime period for the server is about 10 seconds. If the actual period exceeds 10 seconds, OTSTimeout is returned to the client.

    However, if a socket timeout value such as 2 is specified in Tablestore SDK for Java, Tablestore SDK for Java returns SocketTimeoutException.

-   Network transmission latency

    SocketTimeoutException may also be returned if the overall latency is extended by network transmission instead of server-side processing. Check whether traffic and bandwidth usage as well as the packet retransmission rate are high.

-   Frequent full GC

    SocketTimeoutException may be returned when the program runs under heavy load due to frequent full GC. The possible cause is that when full GC occurs, the request cannot be sent or the response cannot be received. SocketTimeoutException is returned when the actual timeout period exceeds the socket timeout value specified in Tablestore SDK for Java.

    If this error occurs, you must use a tool to analyze the GC status of the process to resolve frequent GC.


