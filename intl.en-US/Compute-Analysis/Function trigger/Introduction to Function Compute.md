# Introduction to Function Compute {#concept_u5m_tqj_bfb .concept}

Function Compute is an event-driven service that allows users to write code and upload it without having to manage the operating conditions of the server and so on.

This section introduces how to use Function Compute to perform real-time computation on incremental data in Table Store data tables.

Function Compute prepares and auto-scales to the correct amount of computing resources to run user code. The user pays only for the resources required to run their code. For more information, see [What is Function Compute](https://partners-intl.aliyun.com/help/doc-detail/52895.htm).

Table Store Stream is a data channel used to obtain incremental data in Table Store data tables. By creating Table Store triggers, Table Store Stream and Function Compute can be automatically docked. This allows the custom program logic in the computing function to automatically process changed data in Table Store data tables. For more information, see [Table Store Stream](../../../../reseller.en-US/Functions/Stream/Overview.md#).

This section introduces how to use Function Compute to perform real-time computation on incremental data in Table Store data tables.

## Configuring the Table Store trigger {#section_tff_f1h_dfb .section}

You can use the console to create a Table Store trigger to process a real-time data stream from Table Store data tables.

1.  Create a data table with streaming enabled.
    1.  Create an instance in the [Table Store console](https://partners-intl.console.aliyun.com/#/ots).

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20324/153968011211913_en-US.png)

    2.  Create a data table under this instance and enable Streaming.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20324/153968011211914_en-US.png)

2.  Create a Function Compute function.
    1.  Create a service in the [Function Compute console](https://partners-intl.console.aliyun.com/#/fc).

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20324/153968011211915_en-US.png)

    2.  In the advanced configuration of the service, you can configure the role of the service for the authorization function to collect logs and to continue accessing the user’s other resources in the computing function. For more information, see [User permissions](https://partners-intl.aliyun.com/help/doc-detail/51885.htm).

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20324/153968011211916_en-US.png)

    3.  Click **New Function** under the newly created service.
    4.  Select the **Blank function** on the Function template page.
    5.  On the Trigger configuration page, select **Do not create any triggers** and click **Next**.
    6.  Configure function information.

        Table Store trigger uses the [CBOR format](http://cbor.io/) to encode incremental data as a Function Compute event and calls the user function. The following example function decodes and prints the event to the log center. You can process the data as you wish after the data is decoded.

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20324/153968011311918_en-US.png)

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20323/153968011311930_en-US.png)

3.  Create and test the Table Store trigger.
    1.  Under the newly created data table in the [Table Store console](https://partners-intl.console.aliyun.com/#/ots), select **Use an existing function** to create the trigger.
    2.  During creation, you must authorize Table Store to send event notifications.

        After selecting, you can view the automatically created authorization role AliyunTableStoreStreamNotificationRole in the [RAM console](https://partners-intl.console.aliyun.com/#/ram).

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20324/153968011311919_en-US.png)


## Data processing {#section_ix3_lch_dfb .section}

-   Data format

    Table Store trigger uses the [CBOR format](http://cbor.io/) to encode incremental data to form a Function Compute event. The specific data format of incremental data is as follows:

    ```
    {
        "Version": "string",
        "Records": [
            {
                "Type": "string",
                "Info": {
                    "Timestamp": int64
                },
                "PrimaryKey": [
                    {
                        "ColumnName": "string",
                        "Value": formated_value
                    }
                ],
                "Columns": [
                    {
                        "Type": "string",
                        "ColumnName": "string",
                        "Value": formated_value,
                        "Timestamp": int64
                    }
                ]
            }
        ]
    }
    ```

-   Member definition
    -   Version
        -   Description: The version number of payload, currently Sync-v1
        -   Type: string
    -   Records
        -   Description: Incremental data row array in the data table
        -   Contains:
            -   Type
                -   Description: Data row type, which can be PutRow, UpdateRow, or DeleteRow
                -   Type: string
            -   Info
                -   Description: Basic information about a data row
                -   Contains:
                    -   Timestamp
                    -   Description: The last modified time of the row \(UTC\)
                    -   Type: int64
            -   PrimaryKey
                -   Description: Primary key column array
                -   Contains:
                    -   ColumnName
                        -   Description: Primary key column name
                        -   Type: string
                    -   Value
                        -   Description: Primary key column content
                        -   Type: Formated\_value, which can be integer, string, or blob
            -   Columns
                -   Description: Attribute column array
                -   Contains:
                    -   Type
                        -   Description: Attribute column type, which can be Put, DeleteOneVersion, or DeleteAllVersions
                        -   Type: string
                    -   ColumnName
                        -   Description: Attribute column name
                        -   Type: string
                    -   Value
                        -   Description: Attribute column content
                        -   Type: Formated\_value, which can be integer, boolean, double, string, or blob
                    -   Timestamp
                        -   Description: The last modified time \(UTC\) of the attribute column
                        -   Type: int64
-   Example data

    ```
    {
        "Version": "Sync-v1",
        "Records": [
            {
                "Type": "PutRow",
                "Info": {
                    "Timestamp": 1506416585740836
                },
                "PrimaryKey": [
                    {
                        "ColumnName": "pk_0",
                        "Value": 1506416585881590900
                    },
                    {
                        "ColumnName": "pk_1",
                        "Value": "2017-09-26 17:03:05.8815909 +0800 CST"
                    },
                    {
                        "ColumnName": "pk_2",
                        "Value": 1506416585741000
                    }
                ],
                "Columns": [
                    {
                        "Type": "Put",
                        "ColumnName": "attr_0",
                        "Value": "hello_table_store",
                        "Timestamp": 1506416585741
                    },
                    {
                        "Type": "Put",
                        "ColumnName": "attr_1",
                        "Value": 1506416585881590900,
                        "Timestamp": 1506416585741
                    }
                ]
            }
        ]
    }
    ```


## Online debugging {#section_bnn_ldh_dfb .section}

Function Compute supports online debugging of functions. The user can build the triggered event and test the code logic.

Since the Event of the Table Store trigger function service is in the CBOR format, the data format is a JSON-like binary format that can be debugged online as follows:

1.  Import CBOR and JSON simultaneously in the code.
2.  Click the **Trigger event**, select **Custom**, paste the JSON file from the preceding data example into the edit box, modify it based on requirements, and save it.
3.  In the code, first use `records = json.loads(event)` to process the customized testing trigger event, and then click **Run** to test the code.
4.  When the test of `records=json.loads(event)` succeeds, you can change the code to `records = cbor.loads(event)` and save it. Then, when data is written in Table Store, the related function logic is triggered.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20324/153968011311920_en-US.jpg)


Example code:

```
import logging
import cbor
import json
def get_attrbute_value(record, column):
    attrs = record[u'Columns']
    for x in attrs:
        if x[u'ColumnName'] == column:
            return x['Value']
def get_pk_value(record, column):
    attrs = record[u'PrimaryKey']
    for x in attrs:
        if x['ColumnName'] == column:
            return x['Value']
def handler(event, context):
    logger = logging.getLogger()
    logger.info("Begin to handle event")
    #records = cbor.loads(event)
    records = json.loads(event)
    for record in records['Records']:
        logger.info("Handle record: %s", record)
        pk_0 = get_pk_value(record, "pk_0")
        attr_0 = get_attrbute_value(record, "attr_0")
    return 'OK'
```

