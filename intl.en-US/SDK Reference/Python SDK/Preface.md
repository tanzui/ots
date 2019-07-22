# Preface {#concept_31723_zh .concept}

This document describes how to install and use Python SDK v4.x.x for Table Store. Make sure that you have activated Alibaba Cloud Table Store and created an AccessKeyID and AccessKeySecret.

-   If you have not activated Alibaba Cloud Table Store or want to learn more about this service, visit the [Table Store product homepage](https://www.alibabacloud.com/product/table-store).

-   If you have not created an AccessKeyID and AccessKeySecret, log on to the [AccessKey console](https://ak-console.aliyun.com/#/accesskey) to create an AccessKey.


## Download the SDK {#section_zjm_mv2_2fb .section}

-   [SDK](https://tablestore-doc.oss-cn-hangzhou.aliyuncs.com/aliyun-tablestore-sdk/python/aliyun-tablestore-python-sdk-5.1.0.tar.gz)

-   [GitHub](https://github.com/aliyun/aliyun-tablestore-python-sdk)


For more information about version history, [click here](intl.en-US/SDK Reference/Download SDK/Python SDK.md#).

## Compatibility {#section_i33_sv1_sv8 .section}

-   Table Store is compatible with Python SDK v5.x.x.

-   Table Store is compatible with Python SDK v4.x.x.

-   Table Store is incompatible with Python SDK v2.x.x, because Python SDK v2.x.x supports out-of-order primary keys. Detailed incompatibilities include:

    -   The name of the SDK is changed from ots2 to tablestore.
    -   The Client.create\_table operation is added with the TableOptions parameter.
    -   The primary\_key parameter for the put\_row, get\_row, and update\_row operations is changed from the dict type to the list type to guarantee sequence.
    -   The attribute\_columns parameter for the put\_row and update\_row operations is changed from the dict type to the list type.
    -   The attribute\_columns parameter for the put\_row and update\_row operations is added with timestamp.
    -   The get\_row and get\_range operations are added with the max\_version and time\_range parameters. At lease one parameter must exist.
    -   The put\_row, update\_row, and delete\_row operations are added with the return\_type parameter. Currently, only RT\_PK is supported, indicating that the returned value contains the PK value of the current row.
    -   The values returned by the put\_row, update\_row, and delete\_row operations are added with return\_row. If return\_type is specified as RT\_PK in a request, return\_row contains the PK value of the current row.

## Version {#section_8qn_yqx_7vv .section}

Latest version: 5.1.0

