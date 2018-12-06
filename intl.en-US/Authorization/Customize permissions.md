# Customize permissions {#concept_27362_zh .concept}

## Action {#section_lgx_21t_cfb .section}

Action is an API name that is used to specify APIs that are open or restricted for user access. When creating a Table Store authorization policy, add an `ots:` prefix for each Action and separate multiple Actions using commas. The asterisk \(\*\) wildcard is also supported \(including prefix matching and suffix matching\).

Typical Action

-   Single API

    ```
    "Action": "ots:GetRow"
    
    ```

-   Multiple APIs

    ```
    "Action": [
    "ots:PutRow",
    "ots:GetRow"
    ]
    
    ```

-   All read-only API

    ```
    "Action": [
    "ots:BatchGet*",
    "ots:Describe*",
    "ots:Get*",
    "ots:List*",
    "ots:ComputeSplitPointsBySize"
    ]
    
    ```

-   All read and write API

    ```
    "Action": "ots:*"
    
    ```


## Resource { .section}

A Resource in Table Store is composed of multiple fields including product, region, user ID, instance name, and table name. Each field supports asterisk \(\*\) wildcard \(including prefix matching and suffix matching\).

The format is as follows:

```
acs:ots:[region]:[user_id]:instance/[instance_name]/table/[table_name]

```

-   The product is ots.
-   \[xxx\] indicates a variable.
-   The region is an abbreviation written in English, for example, cn-hangzhou. For more information about regions of service nodes, see [Region](../../../../reseller.en-US/Product Introduction/Terms/Region.md#).
-   The user ID is the Alibaba Cloud account ID.

    **Note:** Instance names are case-insensitive. However, you must use lower case letters for `[instance_name]` in resource definition.


Typical Resource

-   All resources of the users in all regions

    ```
    "Resource": "acs:ots:*:*:*"
    
    ```

-   All instances and their tables of user 123456 in China East 1 region

    ```
    "Resource": "acs:ots:cn-hangzhou:123456:instance/*"
    
    ```

-   Instance abc and its tables of user 123456 in China East 1 region

    ```
    "Resource": [
    "acs:ots:cn-hangzhou:123456:instance/abc",
    "acs:ots:cn-hangzhou:123456:instance/abc/table/*"
    ]
    
    ```

-   All instances whose names begin with abc and their tables

    ```
    "Resource": "acs:ots:*:*:instance/abc*"
    
    ```

-   All instances whose names begin with abc and their tables whose names begin with xyz \(excluding instance resources, and not match acs:ots:\*:\*:instance/abc\*\)

    ```
    "Resource": "acs:ots:*:*:instance/abc*/table/xyz*"
    
    ```

-   All instances whose names end with abc and their tables whose names end with xyz.

    ```
    "Resource": [
    "acs:ots:*:*:instance/*abc",
    "acs:ots:*:*:instance/*abc/table/*xyz"
    ]
    
    ```


## API types { .section}

Table Store has two types of APIs

-   Management APIs for reading from, and writing to, instances.

-   Data APIs for reading from, and writing to, tables and rows.


The following table describes these APIS:

|API/Action|API Type|Description|
|:---------|:-------|:----------|
|ListInstance|Management|Get instance list, called by console only|
|InsertInstance|Management|Create instance, called by console only|
|GetInstance|Management|Get instance meta, called by console only|
|DeleteInstance|Management|Delete instance, called by console only|
|ListTable|Data|Get table list, called by console or SDK|
|CreateTable|Data|Create table, called by console or SDK|
|UpdateTable|Data|Update table meta, called by console or SDK|
|DescribeTable|Data|Get table meta, called by console or SDK|
|DeleteTable|Data|Delete table, called by console or SDK|
|GetRow|Data|Read a record, called by SDK only|
|PutRow|Data|Insert a record, called by SDK only|
|UpdateRow|Data|Update a record, called by SDK only|
|DeleteRow|Data|Delete a record, called by SDK only|
|GetRange|Data|Readrange, called by SDK only|
|BatchGetRow|Data|Batch read records, called by SDK only|
|BatchWriteRow|Data|Batch write records, called by SDK only|

-   Resources accessed by management APIs

    Management APIs are generally instance-related operations and can be called only on the console. The actions and resources definitions of management APIs determine subsequent use of the console. The prefix `acs:ots:[region]:[user_id]:` is omitted in the following accessed resources, leaving only the instance and table parts to be described.

    |API/Action|Resource Access|
    |:---------|:--------------|
    |ListInstance|instance/\*|
    |InsertInstance|instance/\[instance\_name\]|
    |GetInstance|instance/\[instance\_name\]|
    |DeleteInstance|instance/\[instance\_name\]|

-   Resources accessed by data APIs

    Data APIs are genearlly table-related operations and can be called both on the console and by the SDK. The actions and resources definitions of data APIs determine subsequent use of the console. The prefix `acs:ots:[region]:[user_id]:` is omitted in the following accessed resources, leaving only the instance and table parts to be described.

    |API/Action|Resource Access|
    |:---------|:--------------|
    |ListTable|instance/\[instance\_name\]/table/\*|
    |CreateTable|instance/\[instance\_name\]/table/\[table\_name\]|
    |UpdateTable|instance/\[instance\_name\]/table/\[table\_name\]|
    |DescribeTable|instance/\[instance\_name\]/table/\[table\_name\]|
    |DeleteTable|instance/\[instance\_name\]/table/\[table\_name\]|
    |GetRow|instance/\[instance\_name\]/table/\[table\_name\]|
    |PutRow|instance/\[instance\_name\]/table/\[table\_name\]|
    |UpdateRow|instance/\[instance\_name\]/table/\[table\_name\]|
    |DeleteRow|instance/\[instance\_name\]/table/\[table\_name\]|
    |GetRange|instance/\[instance\_name\]/table/\[table\_name\]|
    |BatchGetRow|instance/\[instance\_name\]/table/\[table\_name\]|
    |BatchWriteRow|instance/\[instance\_name\]/table/\[table\_name\]|

-   Limits
    -   In a policy, actions and resources are verified by string matching. When using the asterisk \(\*\) wildcard, prefix matching and suffix matching are distinguished. For example, if a resource is defined as `acs:ots:*:*:instance/*/`, then `acs:ots:*:*:instance/abc` cannot be matched. If a resource is defined as `acs:ots:*:*:instance/abc`, then `acs:ots:*:*:instance/abc/table/xyz` cannot be matched.

    -   To manage instance resources on the Table Store console, you must have permission to read the `acs:ots:[region]:[user_id]:instance/*` resource to obtain the instance list on the console.

    -   For Batch APIs \(such as BatchGetRow and BatchWriteRow\), the backend service performs authentication for each table being accessed. Operations can be performed only when authentication is successful for all tables. Otherwise, a permission error is returned.


## Condition { .section}

The policy supports multiple authentication conditions that are supported on all APIs of Table Store, including access IP address restriction, whether to access through HTTPS, whether to access through Multi-Factor Authentication \(MFA\), and access time restriction.

-   Access IP address restriction

    Resource Access Management can restrict the source IP addresses used to access Table Store, and filter IP addresses based on the network segment. The following are typical application scenarios:

    -   Multiple IP addresses are restricted. For example, only requests from 10.101.168.111 and 10.101.169.111 are allowed.

        ```
        {
        "Statement": [
            {
                "Effect": "Allow",
                "Action": "ots:*",
                "Resource": "acs:ots:*:*:*",
                "Condition": {
                    "IpAddress": {
                        "acs:SourceIp": [
                            "10.101.168.111",
                            "10.101.169.111"
                        ]
                    }
                }
            }
        ],
        "Version": "1"
        }
        
        ```

    -   A single IP address is restricted. For example, only requests from 10.101.168.111 or 10.101.169.111/24 are allowed.

        ```
        {
        "Statement": [
            {
                "Effect": "Allow",
                "Action": "ots:*",
                "Resource": "acs:ots:*:*:*",
                "Condition": {
                    "IpAddress": {
                        "acs:SourceIp": [
                            "10.101.168.111",
                            "10.101.169.111/24"
                        ]
                    }
                }
            }
        ],
        "Version": "1"
        }
        
        ```

-   HTTPS access restriction

    Resource Access Management can specify the use of HTTPS for access.

    Access by requests only through HTTPS

    ```
    {
        "Statement": [
            {
                "Effect": "Allow",
                "Action": "ots:*",
                "Resource": "acs:ots:*:*:*",
                "Condition": {
                    "Bool": {
                        "acs:SecureTransport": "true"
                    }
                }
            }
        ],
        "Version": "1"
    }
    
    ```

-   MFA access restriction

    Resource Access Management can specify the use of MFA for access.

    Access by requests only through MFA

    ```
    {
        "Statement": [
            {
                "Effect": "Allow",
                "Action": "ots:*",
                "Resource": "acs:ots:*:*:*",
                "Condition": {
                    "Bool": {
                        "acs:MFAPresent ": "true"
                    }
                }
            }
        ],
        "Version": "1"
    }
    
    ```

-   Access time restriction

    Resource Access Management can specify the time to grant access by a request, that is, it can determine if access is allowed or rejected by requests only before a specified time. For example,

    user access is allowed only before 00:00:00 January 1, 2016.

    ```
    {
        "Statement": [
            {
                "Effect": "Allow",
                "Action": "ots:*",
                "Resource": "acs:ots:*:*:*",
                "Condition": {
                    "DateLessThan": {
                        "acs:CurrentTime": "2016-01-01T00:00:00+08:00"
                    }
                }
            }
        ],
        "Version": "1"
    }
    
    ```


## Typical application scenarios { .section}

This section defines specific policies in typical scenarios and offers authorization methods.

-   Multiple authorization conditions

    In this scenario, users accessing the 10.101.168.111/24 network segment can read from and write to all instances named online-01 and online-02 \(including all tables of these instances\). A restrictive access policy means access is allowed only before 0:00:00 January 1, 2016 through HTTPS.

    To grant policy permissions to a RAM user, follow these steps:

    1.  Use the primary account to log on to the [RAM console](https://partners-intl.console.aliyun.com/#/ram).
    2.  In the left-side navigation pane, click **Policies**.
    3.  In the upper-right corner, click **Create Authorization Policy**.
    4.  Select **Blank Template**.
    5.  Enter the **Authorization Policy Name** and copy the following content to **Policy Content**.

        ```
        {
        "Statement": [
            {
                "Effect": "Allow",
                "Action": "ots:*",
                "Resource": [
                    "acs:ots:*:*:instance/online-01",
                    "acs:ots:*:*:instance/online-01/table/*",
                    "acs:ots:*:*:instance/online-02",
                    "acs:ots:*:*:instance/online-02/table/*"
                ],
                "Condition": {
                    "IpAddress": {
                        "acs:SourceIp": [
                            "10.101.168.111/24"
                        ]
                    },
                    "DateLessThan": {
                        "acs:CurrentTime": "2016-01-01T00:00:00+08:00"
                    },
                    "Bool": {
                        "acs:SecureTransport": "true"
                    }
                }
            }
        ],
        "Version": "1"
        }
        
        ```

    6.  Click **Create Authorization** Policy and then click **Close**.
    7.  In the left-side navigation pane, click **Users**.
    8.  Locate the RAM user to be authorized, and click **Authorize**.
    9.  Select the policy created in the preceding steps, and click ![](images/11777_en-US.png).
    10. Click **OK**.
-   Reject requests

    In this scenario, users accessing the IP address 10.101.169.111 are not allowed to write to all tables of instances in the Beijing region whose names begin with `online` and `product`. Operations related to instances are not involved.

    To reject requests, first see the preceding steps to create a new policy and grant policy permissions to the designated RAM user. Then, during policy creation, copy the following content to **Policy Content**.

    ```
    {
        "Statement": [
            {
                "Effect": "Deny",
                "Action": [
                    "ots:Create*",
                    "ots:Insert*",
                    "ots:Put*",
                    "ots:Update*",
                    "ots:Delete*",
                    "ots:BatchWrite*"
                ],
                "Resource": [
                    "acs:ots:cn-beijing:*:instance/online*/table/*",
                    "acs:ots:cn-beijing:*:instance/product*/table/*"
                ],
                "Condition": {
                    "IpAddress": {
                        "acs:SourceIp": [
                            "10.101.169.111"
                        ]
                    }
                }
            }
        ],
        "Version": "1"
    }
    
    
    ```


