# Initialization {#concept_56352_zh .concept}

TableStore.Client is the client for Table Store, providing callers with a series of methods for operating tables and reading/writing data from/to a single row or multiple rows.

## Determine an endpoint {#section_pqf_bn2_2fb .section}

An endpoint is the domain name address of Alibaba Cloud Table Store in a region. It supports the following format:

|Endpoint type|Description|
|:------------|:----------|
|Region address|The region of the current Table Store instance, for example, `https://instance.cn-hangzhou.ots.aliyuncs.com`|

Region address of Table Store

To query the endpoint where your Table Store instance is located, follow these steps:

1.  Log on to the [Table Store console](https://ots.console.aliyun.com).
2.  Go to the Instance Details page and locate the instance access address, which is the endpoint of the instance.

## Configure an AccessKey { .section}

To access Alibaba Cloud Table Store, you need a valid AccessKey \(including an AccessKeyId and AccessKeySecret\) for signature authentication. To obtain the AccessKey, follow these steps:

1.  Register an [Alibaba Cloud account](https://account-intl.aliyun.com/register/intl_register.htm).
2.  Log on to the [AccessKey console](https://ak-console.aliyun.com/#/accesskey) to create an AccessKeyId and AccessKeySecret.

After you obtain the AccessKeyId and AccessKeySecret, use the endpoint of Table Store to create a client and initialize a TableStore.Client instance.

**Example:**

```
		var client = new TableStore.Client({
accessKeyId: '<your access key id>',
accessKeySecret: '<your access key secret>',
endpoint: '<your endpoint>',
instancename: '<your instance name>',
maxRetries:20,//The default number of retry attempts is 20. You can ignore this parameter
	});
			
```

