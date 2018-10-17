# Initialization {#reference2616 .reference}

The Table Store client provides callers with a series of methods for operating tables, single rows of data, multiple rows of data, and so on.

## Determine an endpoint { .section}

Endpoint is the domain name address for the Alibaba Cloud Table Store service in each region. Current supported formats include:

|Example|Description|
|:------|:----------|
| `http://sun.cn-hangzhou.ots.aliyuncs.com` |Public network access to the sun instance in Hangzhou using HTTP.|
| `https://sun.cn-hangzhou.ots.aliyuncs.com` |Public network access to the sun instance in Hangzhou using HTTPS.|

**Note:** 

-   Table Store supports public and private network access.
-   You can log on to the [Table Store console](https://partners-intl.console.aliyun.com/#/ots) and go to the instance details page. The Instance Access URL is the endpoint for that instance.

## Configure an AccessKey { .section}

To access the Alibaba Cloud Table Store service, you must have a valid AccessKey for signature authentication. These three ways of configuring an AccessKey are currently supported:

-   Use the AccessKeyId and AccessKeySecret of your primary account. The procedure is as follows:

    1.  Register an Alibaba Cloud account.
    2.  Log on to the [AccessKey console](https://partners-intl.console.aliyun.com/#/ak) to create an AccessKeyId and AccessKeySecret.
-   Use the AccessKeyId and AccesskeySecret of the RAM user authorized to access Table Store. The procedure is as follows:

    1.  Use your primary account to access Resource Access Management \(RAM\) and create a new RAM user or use an existing one.
    2.  Use your primary account to authorize the RAM user to access Table Store.
    3.  You can then use the AccessKeyId and AccessKeySecret of the authorized RAM user to access Table Store.
-   Use an STS token for temporary access. The procedure is as follows:

    1.  The application server accesses the RAM/STS service, obtains a temporary AccesskeyId, AccesskeySecret, and token, and sends them to you.
    2.  You can use the temporary key to access the Table Store service.

## Create a client { .section}

When you use a Table Store SDK, you first have to construct a client and then call its interface to access the Table Store service. The client interface is consistent with the RESTful API provided by Table Store.

**Client type**

Table Store C++ SDK offers two types of client: SyncClient and AsyncClient. These correspond to the synchronous interface and asynchronous interface respectively.

-   Synchronous interface: As soon as the call is completed, the request is executed, making it quite a convenient method. You can use the synchronous interface to learn more about the various Table Store functions.

-   Asynchronous interface: These provide more flexibility than synchronous interfaces. If you have high performance requirements, you can make a trade-off between using asynchronous interfaces and multithreading.


**Note:** Both SyncClient and AsyncClient are thread-safe and can automatically and internally manage threads and connection resources. You do not have to create a client for every thread or request. Instead, create a global client.

**Synchronous interface**

-   Create directly \(use a Table Store endpoint to create a client\)

    ```language-cpp
    Endpoint ep("YourEndpoint", "YourInstance");
    Credential cr("AccessKeyId", "AccessKeySecret");
    ClientOptions opts;
    SyncClient* client = NULL;
    Optional<OTSError> res = SyncClient::create(client, ep, cr, opts);
    
    ```

    **Note:** Avoid using the AccessKey of your primary account to access Table Store. We recommend using a temporary token or the AccessKey of a sub-account. If you use a temporary STS token, the credential object in the aforementioned code must be changed to: `Credential cr("AccessKeyId", "AccessKeySecret", "SecurityToken");`.

    The configuration items included in ClientOptions are described as follows. You can use the default values or define them yourself.

    |mMaxConnections|The maximum number of total connections and also the maximum number of concurrent requests. A persistent connection is maintained between the SDK and the Table Store service. Each new request is sent by a randomly selected idle connection.|
    |mConnectTimeout|Connection timeout. Considering the DNS resolution time, the recommended connection timeout is 10 seconds at least.|
    |mRequestTimeout|Request timeout time.|
    |mRetryStrategy|Retry strategy. By default, the retry strategy retries a failed idempotence request within 10 seconds. You can define your own retry strategy.|
    |mLogger|Logger. By default, the logger exports logs to standard errors. We recommend you define your own logger.|
    |mActors|Thread pool. The thread pool used to run your callback. The default is 10 threads.|

-   Create from AsyncClient

    ```
    AsyncClient& async = ... ;
    SyncClient* sync = SyncClient::create(async);
    ```


**Asynchronous interface**

See [Asynchronous interfaces](reseller.en-US/SDK Reference/C++ SDK/Asynchronous interface.md#) for more information on how to create and use an asynchronous interface client.

## Multithreading {#section_czv_vkf_2fb .section}

Multithreading is supported. When multithreading, we recommend sharing one client object.

