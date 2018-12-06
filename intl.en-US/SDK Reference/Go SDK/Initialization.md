# Initialization {#concept_52254_zh .concept}

TableStoreClient is the client for Table Store. It provides callers with a series of methods for operating tables and reading/writing data from/to a single row or multiple rows.

## Determine an endpoint {#section_fjq_kr2_2fb .section}

An endpoint is the domain name address of Alibaba Cloud Table Store in a region. It supports the following format:

|Endpoint type|Description|
|:------------|:----------|
|Region address|The region of the current Table Store instance, for example, `https://instance.cn-hangzhou.ots.aliyuncs.com` |

**Region address of Table Store**

To query the endpoint where your Table Store instance is located, follow these steps:

1.  Log on to the [Table Store console](https://partners-intl.console.aliyun.com/#/ots).
2.  Go to the Instance Details page and locate the instance access address, which is the endpoint of the instance.

## Configure an AccessKey { .section}

To access Alibaba Cloud Table Store, you need a valid AccessKey \(including an AccessKeyId and AccessKeySecret\) for signature authentication. To obtain the AccessKey, follow these steps:

1.  Register an Alibaba Cloud account.
2.  Log on to the [AccessKey console](https://partners-intl.console.aliyun.com/#/ak) to create an AccessKeyId and AccessKeySecret.

After you obtain the AccessKeyId and AccessKeySecret, initialize a TableStore.Client instance by following these steps:

Use the endpoint of Table Store to create a client.

 **API:** 

```language-go
	// Initialize the "TableStoreClient" instance
	// endPoint is the Table Store address (for example, "https://instance.cn-hangzhou.ots.aliyun.com:80"), which must start with "https://"
	// accessKeyId is the AccessKeyID used to access Table Store.
	// accessKeySecret is the AccessKeySecret used to access Table Store.
	// instanceName is the name of the instance to access. You can create an instance on the Table Store console
func NewClient(endPoint, instanceName, accessKeyId, accessKeySecret string, options ... ClientOption) *TableStoreClient

```

 **Example:** 

```language-go
	client = NewClient("your_instance_endpoint",  "your_instance_name", "your_user_id", "your_user_key")

```

