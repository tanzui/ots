# Initialization {#concept_43009_zh .concept}

The OTSClient is the client for Table Store. It provides callers with a series of methods for operating tables and reading/writing data from/to a single row or multiple rows. To use the Java SDK to initiate a request to Table Store, you must initialize an OTSClient instance and modify the default configurations of the ClientConfiguration as needed.

## Determine an endpoint {#section_nwd_jbd_2fb .section}

An endpoint is the domain of Alibaba Cloud Table Store in a region. It supports the following format.

|Example|Description|
|:------|:----------|
| `http://sun.cn-hangzhou.ots.aliyuncs.com` |Accesses the sun instance in Hangzhou over the Internet using the HTTP protocol.|
| `https://sun.cn-hangzhou.ots.aliyuncs.com` |Accesses the sun instance in Hangzhou over the Internet using the HTTPS protocol.|

**Note:** Instances can also be accessed over the intranet.

To query the endpoint where your Table Store instance is located, follow these steps:

1.  Log on to the [Alibaba Cloud Table Store Console](https://partners-intl.console.aliyun.com/#/ots).
2.  Go to the Instance Details page and locate the Instance Access URL, which is the endpoint of the instance.

## Configure an AccessKey { .section}

To access the Alibaba Cloud Table Store service, you need a valid AccessKey for signature authentication. The following types of AccessKeys is supported:

-   AccessKeyID and AccessKeySecret of the primary account. The creation process is as follows:

    1.  Register an Alibaba Cloud account on the Alibaba Cloud website.
    2.  Log on to the [AccessKey Console](https://partners-intl.console.aliyun.com/#/ak) to apply for an AccessKey.
-   AccessKeyID and AccessKeySecret of the RAM user authorized to access Table Store. The creation process is as follows:

    1.  Use the primary account to access RAM and create a RAM user or use an existing RAM user.
    2.  Use the primary account to authorize the RAM user to access Table Store.

        After authorization, the AccessKeyID and AccessKeySecret of the RAM user can be used to access Table Store.

-   STS token for temporary access. The token acquisition process is as follows:

    1.  The application server accesses the RAM/STS server to obtain a temporary AccessKeyID, AccessKeySecret, and token, and sends them to you.
    2.  Use the temporary AccessKeyID, AccessKeySecret, and token to access Table Store.

## Initialization { .section}

After you obtain the AccessKeyID and AccessKeySecret, you must initialize an OTSClient instance.

Create a client

When using Table Store SDKs, you must construct a client and then call the client API to access the Table Store service. The client API functions are similar to the RESTful API provided by Table Store.

The latest versions of Table Store SDKs provide two types of clients: SyncClient and AsyncClient, which are designed for synchronous APIs and asynchronous APIs respectively.

Once a synchronous API call is completed, the request has been executed. You can call synchronous APIs to learn about the various functions of Table Store. Compared to synchronous APIs, asynchronous APIs provide greater flexibility. If you have high performance requirements, you can choose between the use of asynchronous APIs and multithreading, depending on your business needs.

**Note:** Both SyncClient and AsyncClient are secure threads, which contain management threads and connection resources. We do not recommend creating too many clients. Generally, one global client is sufficient.

Sample code

1.  Use the default configuration to create a SyncClient.

    ```language-java
            final String endPoint = ""; 
            final String accessKeyId = ""; 
            final String accessKeySecret = ""; 
            final String instanceName = "";
    
            SyncClient client = new SyncClient(endPoint, accessKeyId, accessKeySecret, instanceName);
    
    ```

2.  Use custom configuration to create a SyncClient.

    ```
    // ClientConfiguration provides multiple configuration items. Commonly used items are listed as follows.
            ClientConfiguration clientConfiguration = new ClientConfiguration();
            // Set the connection establishment time-out.
            clientConfiguration.setConnectionTimeoutInMillisecond(5000);
            // Set the socket time-out.
            clientConfiguration.setSocketTimeoutInMillisecond(5000);
            // Set the retry policy. If this is not set, the default retry policy is used.
            clientConfiguration.setRetryStrategy(new AlwaysRetryStrategy());
            SyncClient client = new SyncClient(endPoint, accessId, accessKey,
                    instanceName, clientConfiguration);
    ```


## HTTPS { .section}

Upgrade to Java 7 for HTTPS.

## Multithreading { .section}

-   Multithreading is supported.

-   We recommend that multiple threads use the same OTSClient object.


