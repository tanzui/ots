# Prepare the environment {#concept_asr_5ss_lgb .concept}

This topic describes how to build LogHub Shipper for TableStore \(LogHub Shipper\), and describes how LogHub Shipper converts log data in Log Service into structured data and stores the data to Table Store.

## Prerequisites {#section_b5t_wss_lgb .section}

**Log Service**

You have activated Log Service and requested a project and a Logstore. LogHub Shipper does not modify log data from Log Service, and can use the project and Logstore that you have requested. In the following example, the project is lhshipper-test, the Logstore is test-store, and the region is China \(Hangzhou\).

**Table Store**

You have activated Table Store and prepared two tables.

-   The first table is a data table that stores log data synchronized from Log Service. This table has three primary key columns as follows:

    -   rename: the type is STRING.

    -   trans-pkey: the type is INTEGER.

    -   keep: the type is STRING.

-   The second table is a status table for LogHub Shipper. This table stores information about the progress of synchronizing log data from each project and each shard of Log Service. LogHub Shipper for multiple projects and Logstores can share the same status table. We recommend that you set the Time To Live \(TTL\) of this table to one or two days to reduce the usage costs. The status table has four primary key columns as follows:

    -   project\_logstore: the type is STRING.

    -   shard: the type is INTEGER.

    -   target\_table: the type is STRING.

    -   timestamp: the type is INTEGER.


**Access control**

You have requested the AccessKeyId and AccessKeySecret as a Resource Access Management \(RAM\) user.

To secure data, we recommend that you use a RAM user to build LogHub Shipper. You can authorize the RAM user to read log data from Log Service \(AliyunLogReadOnly\) and to write log data to Table Store \(AliyunTableStoreWriteOnlyAccess\).

**ECS and Container Service**

You have activated an Elastic Compute Service \(ECS\) instance and Container Service. You need to create a Pay-As-You-Go ECS instance in follow-up steps.

## Build LogHub Shipper {#section_yz3_fws_lgb .section}

1.  Log on to the [Container Service console](https://cs.console.aliyun.com/).
2.  In the left-side navigation pane, click **Clusters** to go to the Cluster List page.
3.  Click **Create Cluster** in the upper-right corner to go to the Create Cluster page.
4.  To set a cluster, follow these rules:
    -   Try to select a region where Log Service and Table Store are located. Then, you can use a private IP address to avoid the latency and downstream traffic fees in a public network.

    -   Do not select the Swarm Mode Cluster checkbox in the example.

        However, we recommend that you select **Swarm Mode Cluster** in the running environment that your service requires to achieve better performance.

    -   Click **Create** in the example.

        However, we recommend that you click **Add** in the running environment that your service requires to add an existing ECS instance.

    -   LogHub Shipper does not require high-configuration instances. Select **1 Core 1 G** from the Instance Type drop-down list.

    -   LogHub Shipper supports dynamic and horizontal scaling. You can select multiple ECS instances.

    -   LogHub Shipper does not use HTTP to transmit data, and does not expose any port. Do not select the Automatically Create Server Load Balancer checkbox.

5.  Click **Create** in the upper-right corner to create a cluster.

    The cluster has a delay in starting initialization. You can check the cluster status on the Cluster List page.


**Note:** If you have an ECS instance, you can add this instance to the specified cluster. For more information, see [Add an existing ECS instance](../../../../intl.en-US/User Guide/Clusters/Add an existing ECS instance.md).

## Create an application {#section_xdl_1vs_lgb .section}

1.  Log on to the [Container Service console](https://cs.console.aliyun.com/).
2.  In the left-side navigation pane, click **Applications** to go to the Application List page.
3.  Click **Create Application** in the upper-right corner.
4.  To set basic information of the application named **loghub-shipper** in this example, follow these rules:
    -   Select the cluster where you want to create the application from the **Cluster** drop-down list.
    -   We recommend that you select the **Pull Docker Image** checkbox to facilitate follow-up version upgrades.
5.  Click **Create with Image**.

    **Note:** You click Create with Image in the example to describe the core procedure. However, in the running environment that your service requires, an application may contain multiple services. In this case, you can click **Create with Orchestration Template** to easily manage the application.

6.  Configure the application. Follow these rules:
    -   To locate the required image, type **loghub-shipper** in the search text box and click **Global search**.
    -   In the **Environment** field, specify the following variables:

        -   access\_key\_id
        -   access\_key\_secret
        -   loghub: a JSON string that indicates the information of Log Service and that includes endpoint, logstore, and consumer\_group. In this string, consumer\_group can be any character string. Multiple container instances can share consumer\_group for the same LogHub Shipper process. But consumer\_group cannot be repeated for multiple LogHub Shipper processes. This variable is shown as follows:

            ```
            {"endpoint": "https://lhshipper-test.cn-hangzhou.log.aliyuncs.com",
            "logstore": "test-store",
            "consumer_group": "defaultcg"}
            ```

        -   tablestore: a JSON string that indicates the information of Table Store. The variable includes endpoint \(the domain name of the Table Store instance\), instance \(the name of the Table Store instance\), target\_table \(the name of a data table\), and status\_table \(the name of a status table\). This variable is shown as follows:

            ```
            {"endpoint": "https://lhshipper-test.cn-hangzhou.ots.aliyuncs.com",
            "instance": "lhshipper-test",
            "target_table": "loghub_target",
            "status_table": "loghub_status"}
            ```

        -   exclusive\_columns: a JSON string to indicate the field of log data that is not imported to Table Store. This variable is shown as follows:

            ```
            ["__source__","time"]
            ```

        -   transform: a JSON string that indicates format conversion for log data. All log data is string type. For example, you can convert data between the rename attribute column and another attribute. This variable is shown as follows:

            ```
            {"rename": "original",
            "trans-pkey": "(->int 10 original)"}
            ```

        In this example, LogHub Shipper changes the log data in the original field into the rename attribute column in the data table. LogHub Shipper also converts the log data in the original field into decimal integers and saves the data to the trans-pkey attribute column of the data table. For more information about type conversions, see Concept and configuration information.

        **Note:** You do not need to specify primary keys for the data table during the configuration. LogHub Shipper automatically reads schema information of the data table. However, log data or the transform variable must include all primary key fields. Otherwise, the system discards the corresponding log data.

7.  Click **Create**. Service deployment takes a short period of time. You can check the service status on the Service List page.

Then, LogHub Shipper is ready to run.

