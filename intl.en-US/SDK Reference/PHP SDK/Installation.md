# Installation {#concept_31756_zh .concept}

## Environment preparation {#section_a5n_cwf_2fb .section}

-   64-bit PHP 5.5 or later \(required\)

    You can run the php -v command to check the current PHP version. Table Store uses 64-bit integers. However, only the string type can be used to represent the 64-bit integer type in 32-bit PHP. Therefore, Table Store does not support 32-bit PHP. In Windows, PHP versions earlier than PHP 7 are not actually 64-bit. When running PHP in Windows, upgrade PHP to PHP 7 or modify the environment by yourself. We strongly recommend that you use PHP 7 to obtain the optimal performance.

-   cURL extension \(recommended\): You can run the php -m command to check whether cURL extension is installed.

    **Note:** 

    -   In Ubuntu, you can run the sudo apt-get install php-curl command to install cURL extension of PHP through apt-get.
    -   In CentOS, you can run the sudo yum install php-curl command to install cURL extension of PHP through yum.
-   OpenSSL extension \(recommended\): To use HTTPS, install OpenSSL PHP extension.

## Installation methods {#section_xz2_u49_s40 .section}

**Composer**

To install the SDK through Composer, follow these steps:

1.  Run the `composer require aliyun/aliyun-tablestore-sdk-php` command in the root directory of the project or declare the dependency on Table Store PHP SDK in the composer.json file.

    ``` {#codeblock_hxv_uzf_iwv}
    "require": {
        "aliyun/aliyun-tablestore-sdk-php": "~5.0"
    }
    ```

2.  Run the composer install command to install the dependency. After the dependency is installed, check whether your directory structure complies with the following structure:

    ``` {#codeblock_2sv_x0h_gmy}
     .
     ├── app.php
     ├── composer.json
     ├── composer.lock
     └── vendor
    					
    ```

    In the preceding directory structure, app.php indicates your application. The vendor directory contains the dependent library. You must introduce the dependency into app.php.

    ``` {#codeblock_heu_09n_bko}
    require_once __DIR__ . '/vendor/autoload.php';
    					
    ```


**Note:** 

-   If your project has referenced autoload.php, you do not need to introduce it again after adding the SDK dependency.
-   If a network error occurs when you use Composer, you can use the [image source](http://pkg.phpcomposer.com/) of Composer China by running the `composer config -g repositories.packagist composer http://packagist.phpcomposer.com` command.

**Source code package**

You can download the source code package in either of the following ways:

-   Select the required version and download the ZIP package on [GitHub](https://github.com/aliyun/aliyun-tablestore-php-sdk/releases).
-   Click [here](https://tablestore-doc.oss-cn-hangzhou.aliyuncs.com/aliyun-tablestore-sdk/php/aliyun-tablestore-php-sdk-5.0.0.tar.gz) to download the source code package.

## Sample programs {#section_i42_pmy_vp4 .section}

The Table Store PHP SDK provides a variety of sample programs for your reference or use. You can obtain the sample programs by using either of the following methods:

-   Download and decompress the Table Store PHP SDK package, and locate the sample programs in the examples directory.

-   Access the GitHub project for the Table Store PHP SDK at [aliyun-tablestore-php-sdk](https://github.com/aliyun/aliyun-tablestore-php-sdk/tree/master/examples).


To run a sample program, follow these steps:

-   Decompress the downloaded SDK package.

-   Modify the ExampleConfig.php file in the examples directory.

    ``` {#codeblock_d90_f0d_37g}
    EXAMPLE_END_POINT: the AccessKey ID that you obtained from Alibaba Cloud.
    EXAMPLE_ACCESS_KEY_ID: the AccessKey Secret that you obtained from Alibaba Cloud.
    EXAMPLE_ACCESS_KEY_SECRET: the domain name that you use to access the Table Store data center, for example, https://sun.cn-hangzhou.ots.aliyuncs.com.
    EXAMPLE_INSTANCE_NAME: the instance that you use to run the sample program. The sample program is operated in this instance.
    ```

-   Run a sample file in the examples directory separately.

The following table describes the sample files.

|File|Description|
|:---|:----------|
|[NewClient.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/NewClient.php)|Demonstrates how to set the default client.|
|[NewClient2.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/NewClient2.php)|Demonstrates how to customize the client.|
|[NewClientLogClosed.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/NewClientLogClosed.php)|Demonstrates how to disable logging on the client.|
|[NewClientLogDefined.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/NewClientLogDefined.php)|Demonstrates how to customize logging on the client.|
|[CreateTable.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/CreateTable.php)|Demonstrates how to use CreateTable.|
|[DeleteTable.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/DeleteTable.php)|Demonstrates how to use DeleteTable.|
|[DescribeTable.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/DescribeTable.php)|Demonstrates how to use DescribeTable.|
|[ListTable.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/ListTable.php)|Demonstrates how to use ListTable.|
|[UpdateTable.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/UpdateTable.php)|Demonstrates how to use UpdateTable.|
|[ComputeSplitPointsBySize.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/ComputeSplitPointsBySize.php)|Demonstrates how to use ComputeSplitPointsBySize.|
|[PutRow.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/PutRow.php)|Demonstrates how to use PutRow.|
|[PutRowWithColumnFilter.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/PutRowWithColumnFilter.php)|Demonstrates how to use PutRow with conditional update.|
|[UpdateRow1.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/UpdateRow1.php)|Demonstrates how to use PUT in UpdateRow.|
|[UpdateRow2.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/UpdateRow2.php)|Demonstrates how to use DELETE\_ALL in UpdateRow.|
|[UpdateRow3.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/UpdateRow3.php)|Demonstrates how to use DELETE in UpdateRow.|
|[UpdateRowWithColumnFilter.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/UpdateRowWithColumnFilter.php)|Demonstrates how to use UpdateRow with conditional update.|
|[GetRow.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/GetRow.php)|Demonstrates how to use GetRow.|
|[GetRow2.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/GetRow2.php)|Demonstrates how to set column\_to\_get in GetRow.|
|[GetRowWithSingleColumnFilter.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/GetRowWithSingleColumnFilter.php)|Demonstrates how to use GetRow with conditional filtering.|
|[GetRowWithMultipleColumnFilter.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/GetRowWithMultipleColumnFilter.php)|Demonstrates how to use GetRow with complex conditional filtering.|
|[DeleteRow.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/DeleteRow.php)|Demonstrates how to use DeleteRow.|
|[DeleteRowWithColumnFilter.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/DeleteRowWithColumnFilter.php)|Demonstrates how to use DeleteRow with conditional filtering.|
|[PKAutoIncrment.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/PKAutoIncrment.php)|Demonstrates how to use auto-increment columns in detail.|
|[BatchGetRow1.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/BatchGetRow1.php)|Demonstrates how to use BatchGetRow to read multiple rows from a table.|
|[BatchGetRow2.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/BatchGetRow2.php)|Demonstrates how to use BatchGetRow to read multiple rows from multiple tables.|
|[BatchGetRow3.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/BatchGetRow3.php)|Demonstrates how to use BatchGetRow to read multiple rows from a table with the specified columns.|
|[BatchGetRow4.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/BatchGetRow4.php)|Demonstrates how to use BatchGetRow to process returned results.|
|[BatchGetRowWithColumnFilter.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/BatchGetRowWithColumnFilter.php)|Demonstrates how to use BatchGetRow with conditional filtering.|
|[BatchWriteRow1.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/BatchWriteRow1.php)|Demonstrates how to perform multiple PUT operations in BatchWriteRow.|
|[BatchWriteRow2.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/BatchWriteRow2.php)|Demonstrates how to perform multiple UPDATE operations in BatchWriteRow.|
|[BatchWriteRow3.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/BatchWriteRow3.php)|Demonstrates how to perform multiple DELETE operations in BatchWriteRow.|
|[BatchWriteRow4.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/BatchWriteRow4.php)|Demonstrates how to simultaneously perform the UPDATE, PUT, and DELETE operations in BatchWriteRow.|
|[BatchWriteRowWithColumnFilter.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/BatchWriteRowWithColumnFilter.php)|Demonstrates how to use BatchWriteRow with conditional update.|
|[GetRange1.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/GetRange1.php)|Demonstrates how to use GetRange.|
|[GetRange2.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/GetRange2.php)|Demonstrates how to use GetRange to obtain the specified columns.|
|[GetRange3.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/GetRange3.php)|Demonstrates how to use GetRange to obtain the specified number of rows.|
|[GetRangeWithColumnFilter.php](https://github.com/aliyun/aliyun-tablestore-php-sdk/blob/master/examples/GetRangeWithColumnFilter.php)|Demonstrates how to use GetRange with conditional filtering.|

