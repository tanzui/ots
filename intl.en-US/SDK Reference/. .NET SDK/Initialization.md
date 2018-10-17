# Initialization {#reference1878 .reference}

The OTSClient is the client for Table Store. It provides callers with a series of methods for operating tables and reading/writing data from/to a single row or multiple rows.

## Determine an endpoint {#section_ikt_fcf_2fb .section}

An endpoint is the domain of Alibaba Cloud Table Store in a region. It supports the following format.

|Endpoint type|Description|
|:------------|:----------|
|Region address|Address of the region where a Table Store instance is located, for example, `https://instance.cn-hangzhou.ots.aliyuncs.com`.|

**Region address of Table Store**

To query the endpoint where your Table Store instance is located, follow these steps:

1.  Log on to the [Table Store console](https://partners-intl.console.aliyun.com/#/ots).
2.  Go to the Instance Details page and locate the **Instance Access URL**, which is the endpoint of the instance.

## Configure an AccessKey { .section}

To access the Alibaba Cloud Table Store service, you need a valid AccessKey \(including an AccessKeyID and AccessKeySecret\) for signature authentication. To obtain the AccessKey, follow these steps:

1.  Register an Alibaba Cloud account on the Alibaba Cloud website.
2.  Log on to the [AccessKey console](https://partners-intl.console.aliyun.com/#/ak) to apply for an AccessKey.

After you obtain the AccessKeyID and AccessKeySecret, use the endpoint of the Table Store to create a client and initialize an OTSClient instance:

 **API:** 

```language-csharp
	/// <summary>
	/// OTSClient constructor.
	/// </summary>
	/// <param name="endPoint">Address of the Table Store service (for example, 'https://instance.cn-hangzhou.ots.aliyun.com:80'). It must start with 'https://'. </param>
	/// <param name="accessKeyID">AccessKeyID of Table Store. </param>
	/// <param name="accessKeySecret">AccessKeySecret of Table Store. </param>
	/// <param name="instanceName">Name of your Table Store instance, which must be created on the Alibaba Cloud Table Store Console. </param>
public OTSClient(string endPoint, string accessKeyID, string accessKeySecret, string instanceName);

	/// <summary>
	/// Create an OTSClient instance using the OTSClientConfig instance.
	/// </summary>
	/// <param name="config">OTSClientConfig instance</param>
	public OTSClient(OTSClientConfig config);

```

 **Example:** 

```language-csharp
	// Construct an OTSClientConfig object
	var config = new OTSClientConfig(Endpoint, AccessKeyId, AccessKeySecret, InstanceName);

	// Disable log output (this function is enabled by default)
	config.OTSDebugLogHandler = null;
	config.OTSErrorLogHandler = null;

	// Create an OTSClient object using OTSClientConfig
	var otsClient = new OTSClient(config);

	// Use the OTSClient to insert or query data

```

**Note:** 

-   You can set ConnectionLimit in the OTSClientConfig object. If ConnectionLimit is not set, the default value is 300.

-   OTSDebugLogHandler and OTSErrorLogHandler in OTSClientConfig control the logging behavior, and are configurable.

-   RetryPolicy in OTSClientConfig controls the retry logic. A default retry policy is provided. You can define your own retry policy.


## Multithreading { .section}

-   Multithreading is supported.

-   We recommend that multiple threads use the same OTSClient object.


