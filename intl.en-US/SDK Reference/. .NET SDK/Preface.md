# Preface {#concept_31699_zh .concept}

## Introduction {#section_w2b_mz2_2fb .section}

This document describes how to install and use C\# SDK 3.0.0 for Table Store. This document assumes that you have already activated Alibaba Cloud Table Store and created an AccessKeyID and AccessKeySecret.

-   If you have not activated Table Store or want to know about the service, visit the Table Store product homepage.

-   If you have not created an AccessKeyID and AccessKeySecret, log on to the [Alibaba Cloud Access Key console](https://partners-intl.console.aliyun.com/#/ak) to create an AccessKey.


## Download the SDK package { .section}

-    [SDK package](https://ots-public-sdk.oss-cn-hangzhou.aliyuncs.com/aliyun-tablestore-dotnet-sdk-3.0.0.zip) 

-    [GitHub](https://github.com/aliyun/aliyun-tablestore-csharp-sdk) 


For more information about version iterations, [click here](reseller.en-US/SDK Reference/Download SDK/. .Net SDK.md#).

## Change updates and compatibility { .section}

For latest version 3.0.0

-   Filters are added.

-   The warnings generated during the compilation process are cleared.

-   The useless dependent packages are cleared.

-   The useless code is cleared.

-   The template-called code is simplified.

-   The invalid parameter checking is added.

-   The user-configured parameters are trimmed.

-   The UserAgent header is added.

-   The DLL file name Aliyun.dll is changed to Aliyun.TableStore.dll.

-   The open source code is released to GitHub.


For SDKs V2.x.x:

-   Incompatibility with partial interfaces: `Condition.IGNORE`, `Condition.EXPECT_EXIST`, and `Condition.EXPECT_NOT_EXIST` are deleted.

-   The DLL file name Aliyun.dll is changed to Aliyun.TableStore.dll.


