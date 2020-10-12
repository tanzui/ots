# What do I do if the "Request denied because this instance can only be accessed from the binded VPC" exception occurs when I use Tablestore SDK?

This topic describes the problem description, cause, and solution of the "Request denied because this instance can only be accessed from the binded VPC" exception when you use Tablestore SDK.

## Problem description

The following exception occurs when you use SDKs to access an instance:

```
[ErrorCode]:OTSAuthFailed, [Message]:Request denied because this instance can only be accessed from the binded VPC.
```

## Cause

After you bind a VPC to a Tablestore instance, you can access the instance only from the address of the bound VPC.

## Solution

After you bind a VPC to a Tablestore instance, you must use the address of the bound VPC to access the instance by using SDKs.

Log on to the Tablestore console. Go to the Network Management tab to obtain the address of the bound VPC.

![fig_vpc](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/4770742061/p133644.png)

