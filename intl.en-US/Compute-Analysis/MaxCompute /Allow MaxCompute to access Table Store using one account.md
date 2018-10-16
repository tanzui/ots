# Allow MaxCompute to access Table Store using one account {#concept_omw_smj_bfb .concept}

## Background information {#section_u2y_dqh_dfb .section}

This document explains how to establish a seamless connection between Table Store and MaxCompute under an Alibaba Cloud account.

As a big data computing service, MaxCompute provides a fast and fully-hosted PB-level data warehousing solution, which allows you to analyze and process mass data economically and efficiently. You can use a simple DDL statement to create an external table on MaxCompute and associate MaxCompute tables with external data sources to provide various data access and output capabilities. MaxCompute tables can contain only structured data while external tables can contain either structured or non-structured data.

Both Table Store and MaxCompute have their own type systems, and the following table shows their mappings.

|Table Store|MaxCompute|
|:----------|:---------|
|STRING|STRING|
|INTEGER|BIGINT|
|DOUBLE|DOUBLE|
|BOOLEAN|BOOLEAN|
|BINARY|BINARY|

## Preparations {#section_jgw_pqh_dfb .section}

Preparations before accessing Table Store using MaxCompute:

1.  Activate the MaxCompute service.
2.  Create a [MaxCompute project](https://partners-intl.aliyun.com/help/doc-detail/30263.htm).
3.  Create an [AccessKey](https://partners-intl.aliyun.com/help/doc-detail/53045.htm).
4.  In the RAM console, authorize MaxCompute to access Table Store.
    -   Method 1: Log on with an Alibaba Cloud account and [click here for quick authorization](https://partners-intl.console.aliyun.com/#/ram).

    -   Method 2: Perform manual authorization using the following steps.

        1.  Log on to the [RAM console](https://partners-intl.console.aliyun.com/#/ram).
        2.  On the Roles page, create a user role AliyunODPSDefaultRole.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20327/153968017811954_en-US.png)

        3.  On the Role Details page, set the policy content.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20327/153968017811955_en-US.png)

            The policy content is set as follows.

            ```
            {
            "Statement": [
            {
            "Action": "sts:AssumeRole",
            "Effect": "Allow",
            "Principal": {
             "Service": [
               "odps.aliyuncs.com"
             ]
            }
            }
            ],
            "Version": "1"
            }
            ```

        4.  On the Policies page, create an authorization policy AliyunODPSRolePolicy.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20327/153968017811956_en-US.png)

            The policy content is set as follows.

            ```
            {
            "Version": "1",
            "Statement": [
             {
               "Action": [
                 "ots:ListTable",
                 "ots:DescribeTable",
                 "ots:GetRow",
                 "ots:PutRow",
                 "ots:UpdateRow",
                 "ots:DeleteRow",
                 "ots:GetRange",
                 "ots:BatchGetRow",
                 "ots:BatchWriteRow",
                 "ots:ComputeSplitPointsBySize"
               ],
               "Resource": "*",
               "Effect": "Allow"
             }
            ]
            }
            You can also customize other permissions.
            ```

        5.  On the Roles page, grant the AliyunODPSRolePolicy permission to the role AliyunODPSDefaultRole.

            ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20327/153968017811957_en-US.png)

5.  [Create an instance](../../../../reseller.en-US/Quick Start/Create an instance.md#) and [create a table](../../../../reseller.en-US/Quick Start/Create a table.md#) in the Table Store console.

    In this example, the Table Store instance and data table are detailed as follows:

    -   Instance name: cap1
    -   Data table name: vehicle\_track
    -   Primary key information: vid \(integer\), gt \(integer\)
    -   Endpoint: `https://cap1.cn-hangzhou.ots-internal.aliyuncs.com`

        **Note:** We recommend that you use the private network address of Table Store when accessing Table Store using MaxCompute.

    -   Set the network type of the instance to **Any Network**.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20327/153968017811958_en-US.png)


## Step 1. Install and configure the client {#section_v4j_ssh_dfb .section}

1.  Download and extract the [MaxCompute client](http://repo.aliyun.com/download/odpscmd/latest/odpscmd_public.zip?spm=a2c4g.11186623.2.28.447730b3NxdBQA&file=odpscmd_public.zip).

    **Note:** Make sure JRE 1.7 or a later version is installed on your computer.

2.  Edit the conf/odps\_config.ini file to configure the client.

    ```
    access_id=*******************
    access_key=*********************
     # The AccessID and AccessKey of your Alibaba Cloud account can be viewed on the "Management Console" > "AccessKeys" page on the Alibaba Cloud official website.
    project_name=my_project
     # Specify the project space you want to access.
    end_point=https://service.odps.aliyun.com/api
     # The access link of the MaxCompute service.
    tunnel_endpoint=https://dt.odps.aliyun.com
     # The access link of the MaxCompute Tunnel service.
    log_view_host=http://logview.odps.aliyun.com
     # After you run a job, the client returns the LogView address of the job. Access the address to see the details of the executed job.
    https_check=true
     # Determine whether to enable HTTPS access.
    ```

    **Note:** In odps\_config.ini file, `#` is used as annotation. In the MaxCompute client, `--` is used as annotation.

3.  Run bin/odpscmd.bat, and enter show tables;.

    The display of tables in the current MaxCompute project indicates that the preceding configuration is correct.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20327/153968017811959_en-US.png)


## Step 2. Create an external table {#section_qkd_hth_dfb .section}

Create a MaxCompute data table \(ots\_vehicle\_track\) and associate it with a table \(vehicle\_track\) in Table Store.

In this example, the associated table is detailed as follows:

-   Instance name: cap1
-   Table name: vehicle\_track
-   Primary Key information: vid \(int\); gt \(int\)
-   Endpoint: `https://cap1.cn-hangzhou.ots-internal.aliyuncs.com`

```
CREATE EXTERNAL TABLE IF NOT EXISTS ots_vehicle_track
(
vid bigint,
gt bigint,
longitude double,
latitude double,
distance double ,
speed double,
oil_consumption double
)
STORED BY 'com.aliyun.odps.TableStoreStorageHandler' -- (1)
WITH SERDEPROPERTIES ( -- (2)
'tablestore.columns.mapping'=':vid, :gt, longitude, latitude, distance, speed, oil_consumption', -- (3)
'tablestore.table.name'='vehicle_track' -- (4)
)
LOCATION 'tablestore://cap1.cn-hangzhou.ots-internal.aliyuncs.com'; -- (5)
```

Parameters are described as follows.

|No.|Parameter|Description|
|:--|:--------|:----------|
|\(1\)|com.aliyun.odps.TableStoreStorageHandler|StorageHandler built in MaxCompute for processing Table Store data. It defines the interactions between MaxCompute and Table Store, and related logics are implemented by MaxCompute.|
|\(2\)|SERDEPROPERITES|The interface that provides parameter options. When TableStoreStorageHandler is used, two options, tablestore.columns.mapping and tablestore.table.name, must be specified.|
|\(3\)|tablestore.columns.mapping|Required option. Table Store columns to be accessed by MaxCompute include primary key and attribute columns. In Table Store, columns with `:`, such as :vid and :gt in this example, are primary key columns, and others are attribute columns. When specifying a mapping, you must provide all primary key columns of the specified Table Store table, and the attribute columns to be accessed by MaxCompute.

|
|\(4\)|tablestore.table.name|The name of Table Store tables to be accessed. If the specified Table Store table name is incorrect \(it does not exist\), an error is reported. MaxCompute does not create any Table Store table proactively.|
|\(5\)|LOCATION|The instance information of Table Store to be accessed, including the instance name and endpoint.|

## Step 3. Access Table Store data through external tables {#section_tlr_1j3_dfb .section}

When an external table is created, Table Store data is introduced into the MaxCompute ecosystem and can be accessed using MaxCompute SQL commands.

```
// Provides statistics on the average speed and oil consumption of vehicles with a VID less than 4 before the time stamp 1469171387)
select vid,count(*),avg(speed),avg(oil_consumption) from ots_vehicle_track where vid <4 and gt<1469171387  group by vid;
```

A result similar to the following is returned.

![](images/11960_en-US.jpeg)

