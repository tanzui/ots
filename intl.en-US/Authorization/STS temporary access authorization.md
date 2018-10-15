# STS temporary access authorization {#concept_27364_zh .concept}

This topic describes how to use STS to grant users temporary permissions for accessing Table Store to better restrict access permissions for short-term RAM user requirements.

Assume that a developer’s app is used by multiple users, and each user is allowed to write data to instance ram\_test\_app. Data upload permissions must therefore be granted to these users, and the data stored by multiple users need to be separated.

To meet these requirements, use STS to grant users temporary access permission. The process is as follows.

## Create roles {#section_zzy_2ms_cfb .section}

1.  Create a new RAM user named ram\_test\_app and do not grant it permissions. For RAM user creation details, see step 1 to step 7 in Use case.
2.  Create two roles, RamTestAppReadOnly and RamTestAppWrite, to perform read operations and to upload files respectively.
    1.  Log on to the [RAM console](https://partners-intl.aliyun.com/#/ram).
    2.  Select **Roles** \> **Create Role**.
    3.  Select **User Role**.
    4.  Enter the role information. Retain the default parameters and click **Next**.
    5.  Enter the role name RamTestAppReadOnly and click **Create**.
    6.  Click **Close**.
3.  When created, the role does not have any permissions. To enable permissions, create a custom authorization policy.
    1.  Select **Policies** \> **Create Authorization Policy**.
    2.  Select **Blank Template**.
    3.  Enter the **Authorization Policy Name**. For example: ram-test-app-readonly, and enter Policy Content as follows.

        ```
        {
        "Statement": [
        	{
        	  "Effect": "Allow",
        	  "Action": [
        		"ots:BatchGet*",
        		"ots:Describe*",
        		"ots:Get*",
        		"ots:List*"
        	  ],
        	  "Resource": [
        		"acs:ots:*:*:instance/ram-test-app",
        		"acs:ots:*:*:instance/ram-test-app/table/*"
        	  ]
        	}
        ],
        "Version": "1"
        }
        
        ```

        In the policy, the role is granted the read-only permission for ram-test-app.

    4.  Click **Create Authorization Policy** and then click **Close**.
4.  Grant the role RamTestAppReadOnly the ram-test-app read-only permission.
    1.  Click Roles and click **Authorize** on the right side of role RamTestAppReadOnly.
    2.  Select ram-test-app-readonly and click ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20297/153960255411777_en-US.png).

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20297/153960255511784_en-US.png)

    3.  Click **OK**.

        Role RamTestAppReadOnly has been granted the read-only permission for ram-test-app.

5.  Follow the preceding steps to create another role called RamTestAppWrite, and grant this role the write permission for ram-test-app. The authorization policy is as follows.

    ```
    	{
     "Statement": [
    		{
    		  "Effect": "Allow",
    		  "Action": [
    			"ots:Create*",
    			"ots:Insert*",
    			"ots:Put*",
    			"ots:Update*",
    			"ots:Delete*",
    			"ots:BatchWrite*"
    		  ],
    		  "Resource": [
    			"acs:ots:*:*:instance/ram-test-app",
    			"acs:ots:*:*:instance/ram-test-app/table/*"
    		  ]
    		}
     ],
     "Version": "1"
    	}
    
    ```


## Temporary access authorization { .section}

-   Preparations

    Authorization is required for assuming roles, otherwise, any RAM users could assume these roles. To assume corresponding roles, a RAM user needs to have designated permissions explicitly configured. To create two policies and grant them to a RAM user \(in this example, ram\_test\_app\), follow these steps:

    1.  In the left-side navigation pane, click **Policies**.
    2.  Click **Create Authorization Policy**.
    3.  Select **Blank Template**.
    4.  Enter the **Authorization Policy Name** and **Policy Content** as follows.

        -   AliyunSTSAssumeRolePolicy2016011401
        ```
        {
        "Version": "1",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": "sts:AssumeRole",
                "Resource": "acs:ram:1983407596944237:role/ramtestappreadonly"
            }
        ]
        }
        
        ```

        -   AliyunSTSAssumeRolePolicy2016011402
        ```
        {
        "Version": "1",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": "sts:AssumeRole",
                "Resource": "acs:ram:1983407596944237:role/ramtestappwrite"
            }
        ]
        }
        
        ```

        **Note:** The content entered after Resource is a role ID. To find the role ID, click **Manage** on the Roles page.

    5.  Click **Users**.
    6.  Find `AliyunSTSAssumeRolePolicy2016011401` and `AliyunSTSAssumeRolePolicy2016011402`, and click ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20297/153960255411777_en-US.png) to add them.
-   Use STS

    [Download STS command line tool Python](https://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/cn/oss/0.4.30/assets/tool/sts.py).

    For more information about the call method and parameter description, see [STS Help Documentation](https://partners-intl.aliyun.com/help/doc-detail/28756.htm).

    ```
    $python ./sts.py AssumeRole RoleArn=acs:ram::1983407596944237:role/ramtestappreadonly RoleSessionName=usr001 Policy='{"Version":"1","Statement":[{"Effect":"Allow","Action":["ots:ListTable","ots:DescribeTable"],"Resource":["acs:ots:*:*:ram-test-app","acs:ots:*:*:ram-test-app/*"]}]}' DurationSeconds=1000 --id=id --secret=secret
    
    ```

    The parameters are described as follows:

    |Parameter|Description|
    |:--------|:----------|
    |RoleArn|The ID of the role to be assumed. A Role ID can be queried in Roles \> **Manage**.|
    |RoleSessionName|The name of a temporary credential. Generally, we suggest separating RoleSessionNames by different application users.|
    |Policy|An additional permission restriction added when the role is assumed.**Note:** Policy is used to restrict the temporary credential permissions after a role is assumed. Temporary credential permissions are the overlapping permissions of the role and the policy passed in. When a role is assumed, a policy can be passed in to improve flexibility. For example, when uploading files, you can add different uploading path restrictions for different users.

|
    |DurationSeconds|The validity period of the temporary credentials, measured in seconds. The minimum value is 900, and the maximum value is 3600.|
    |id and secret|The AccessKey of the role to be assumed.|

-   Test STS functions

    Create a table named test\_write\_read on the [console](https://partners-intl.console.aliyun.com/#/ots). Set the primary key to name and the type to string. Use the Table Store CLI tool to test read and write operations.

    In this example, the RAM user ram\_test\_app is used to access Table Store. In actual scenarios, replace the following AccessKey with your own AccessKey.

    ```
    python2.7 ots_console --url https://TableStoreTest.cn-hangzhou.ots.aliyuncs.com --id 6iTlV1uhiY71mlRt --key clkkuDiq69IJWJ7PnA9PXJxhRWMr3P
    You cannot access the instance!
    ErrorCode: OTSNoPermissionAccess
    ErrorMessage: You have no permission to access the requested resource, please contact the resource owner.
    
    ```

    Without access permission, access attempts using the RAM user ram\_test\_app fail.

-   Use temporary authorization to write data

    In this example, the policy passed in is the same as the role policy. The expiration time is set to 3,600s, and SessionName is assumed as session001.

    1.  Use STS to obtain temporary credentials.

        ```
        python2.7 ./sts.py AssumeRole RoleArn=acs:ram::1983407596944237:role/ramtestappwrite RoleSessionName=session001 Policy='{"Statement": [{"Effect": "Allow","Action": ["ots:Create*","ots:BatchWrite*","ots:Put*","ots:Insert*","ots:Update*","ots:Delete*"],"Resource": ["acs:ots:*:*:instance/ram-test-app","acs:ots:*:*:instance/ram-test-app/table/*"]}],"Version": "1"}' --id=6iTlV1uhiY71mlRt --secret=clkkuDiq69IJWJ7PnA9PXJxhRWMr3P
        {
        "AssumedRoleUser": {
            "Arn": "acs:ram::1983407596944237:role/ramtestappwrite/session001", 
            "AssumedRoleId": "330629052749595885:session001"
        }, 
        "Credentials": {
            "AccessKeyId": "STS.x4gG7KMsfHckQe8nPKLO", 
            "AccessKeySecret": "IA6CJh5kE5J5m8mR6aQXWbMemSL63Xh7SIhrEcke", 
            "Expiration": "2016-01-14T07:58:14Z", 
            "SecurityToken": "CAESgAQIARKAATDsbhiBSujhVEHoMKm1i17pyZhPTCe1BnVF5YzdNyRos4WuQjalxLkOE/hNNxg25vTo9bljKg4VCcrfh6GkJNujMMcJ4V1i/0RMDLfXwa0/vOHP9W/oSQpwAD5EaWJfqVY/nxwmJ0aKJDHPmSieWssnlmocaOZAgHkpCqQSSDA8GhhTVFMueDRnRzdLTXNmSGNrUWU4blBLTE8iEjMzMDYyOTA1Mjc0OTU5NTg4NSoGdXNyMDAxMPnCkfmjKjoGUnNhTUQ1QuIBCgExGtwBCgVBbGxvdxJnCgxBY3Rpb25FcXVhbHMSBkFjdGlvbhpPCgtvdHM6Q3JlYXRlKgoPb3RzOkJhdGNoV3JpdGUqCghvdHM6UHV0KgoLb3RzOkluc2VydCoKC290czpVcGRhdGUqCgtvdHM6RGVsZXRlKhJqCg5SZXNvdXJjZUVxdWFscxIIUmVzb3VyY2UaTgohYWNzOm90czoqOio6aW5zdGFuY2UvcmFtLXRlc3QtYXBwCilhY3M6b3RzOio6KjppbnN0YW5jZS9yYW0tdGVzdC1hcHAvdGFibGUvKkoQMTk4MzQwNzU5Njk0NDIzN1IFMjY4NDJaD0Fzc3VtZWRSb2xlVXNlcmAAahIzMzA2MjkwNTI3NDk1OTU4ODVyD3JhbXRlc3RhcHB3cml0ZQ=="
        }, 
        "RequestId": "5F92B248-F200-40F8-A05A-C9C7D018E351"
        }
        
        ```

    2.  Use Table Store CLI tool to write data. \(The Token parameter is supported by Table Store CLI tool v1.2.\)

        ```
        	python2.7 ots_console --url https://TableStoreTest.cn-hangzhou.ots.aliyuncs.com --id STS.x4gG7KMsfHckQe8nPKLO --key IA6CJh5kE5J5m8mR6aQXWbMemSL63Xh7SIhrEcke --token=CAESgAQIARKAATDsbhiBSujhVEHoMKm1i17pyZhPTCe1BnVF5YzdNyRos4WuQjalxLkOE/hNNxg25vTo9bljKg4VCcrfh6GkJNujMMcJ4V1i/0RMDLfXwa0/vOHP9W/oSQpwAD5EaWJfqVY/nxwmJ0aKJDHPmSieWssnlmocaOZAgHkpCqQSSDA8GhhTVFMueDRnRzdLTXNmSGNrUWU4blBLTE8iEjMzMDYyOTA1Mjc0OTU5NTg4NSoGdXNyMDAxMPnCkfmjKjoGUnNhTUQ1QuIBCgExGtwBCgVBbGxvdxJnCgxBY3Rpb25FcXVhbHMSBkFjdGlvbhpPCgtvdHM6Q3JlYXRlKgoPb3RzOkJhdGNoV3JpdGUqCghvdHM6UHV0KgoLb3RzOkluc2VydCoKC290czpVcGRhdGUqCgtvdHM6RGVsZXRlKhJqCg5SZXNvdXJjZUVxdWFscxIIUmVzb3VyY2UaTgohYWNzOm90czoqOio6aW5zdGFuY2UvcmFtLXRlc3QtYXBwCilhY3M6b3RzOio6KjppbnN0YW5jZS9yYW0tdGVzdC1hcHAvdGFibGUvKkoQMTk4MzQwNzU5Njk0NDIzN1IFMjY4NDJaD0Fzc3VtZWRSb2xlVXNlcmAAahIzMzA2MjkwNTI3NDk1OTU4ODVyD3JhbXRlc3RhcHB3cml0ZQ==
        
        	OTS-TableStoreTest>$ put test_write_read '001' age:integer=30
        	A new row has been put in table test_write_read
        
        ```

-   Use temporary authorization to read data

    In this example, the policy passed in is the same as the role policy. The expiration time is set to 3,600s, and SessionName is assumed as session002.

    1.  Use STS to obtain temporary credentials.

        ```
        python2.7 ./sts.py AssumeRole RoleArn=acs:ram::1983407596944237:role/ramtestappreadonly RoleSessionName=session002 Policy='{"Statement": [{"Effect": "Allow","Action": ["ots:BatchGet*","ots:Describe*","ots:Get*","ots:List*"],"Resource": ["acs:ots:*:*:instance/ram-test-app","acs:ots:*:*:instance/ram-test-app/table/*"]}],"Version": "1"}' --id=6iTlV1uhiY71mlRt --secret=clkkuDiq69IJWJ7PnA9PXJxhRWMr3P
        {
        "AssumedRoleUser": {
            "Arn": "acs:ram::1983407596944237:role/ramtestappreadonly/session002",
            "AssumedRoleId": "396025752746614078:session002"
        },
        "Credentials": {
            "AccessKeyId": "STS. 0qJ2UE8AalcHdQ6n2Q8Q",
            "AccessKeySecret": "pSaUjb8O9mU5M76nkC6FHt6wKwbCVYO27gxSEBAu",
            "Expiration": "2016-01-14T08:14:16Z",
            "SecurityToken": "CAES6wMIARKAAVtHeNgUnhk132OwDfxZTu8gPQCxfakYLeWha/FxoEYNqBKQTtyI4WPC5mpYuu8+n+yamSYTI2VPQ/z44fcYCNT1bQ0km87F3nb6EJxVvCdJIPNGVwQBMdQl/FLwBVhEGJ9BIwog4fMzwhERjqnAP8HbynAIQpG55BHaIXmv53x+GhhTVFMuMHFKMlVFOEFhbGNIZFE2bjJROFEiEjM5NjAyNTc1Mjc0NjYxNDA3OCoKc2Vzc2lvbjAwMjConMz5oyo6BlJzYU1ENULGAQoBMRrAAQoFQWxsb3cSSwoMQWN0aW9uRXF1YWxzEgZBY3Rpb24aMwoNb3RzOkJhdGNoR2V0KgoNb3RzOkRlc2NyaWJlKgoIb3RzOkdldCoKCW90czpMaXN0KhJqCg5SZXNvdXJjZUVxdWFscxIIUmVzb3VyY2UaTgohYWNzOm90czoqOio6aW5zdGFuY2UvcmFtLXRlc3QtYXBwCilhY3M6b3RzOio6KjppbnN0YW5jZS9yYW0tdGVzdC1hcHAvdGFibGUvKkoQMTk4MzQwNzU5Njk0NDIzN1IFMjY4NDJaD0Fzc3VtZWRSb2xlVXNlcmAAahIzOTYwMjU3NTI3NDY2MTQwNzhyEnJhbXRlc3RhcHByZWFkb25seQ=="
        }, 
        "RequestId": "EE788165-B760-4014-952C-E58ED229C80D"
        }
        
        ```

    2.  Use the Table Store CLI tool to read data. \(The Token parameter is supported by Table Store CLI tool v1.2.\)

        ```
        	python2.7 ots_console --url https://TableStoreTest.cn-hangzhou.ots.aliyuncs.com --id STS. 0qJ2UE8AalcHdQ6n2Q8Q --key pSaUjb8O9mU5M76nkC6FHt6wKwbCVYO27gxSEBAu --token=CAES6wMIARKAAVtHeNgUnhk132OwDfxZTu8gPQCxfakYLeWha/FxoEYNqBKQTtyI4WPC5mpYuu8+n+yamSYTI2VPQ/z44fcYCNT1bQ0km87F3nb6EJxVvCdJIPNGVwQBMdQl/FLwBVhEGJ9BIwog4fMzwhERjqnAP8HbynAIQpG55BHaIXmv53x+GhhTVFMuMHFKMlVFOEFhbGNIZFE2bjJROFEiEjM5NjAyNTc1Mjc0NjYxNDA3OCoKc2Vzc2lvbjAwMjConMz5oyo6BlJzYU1ENULGAQoBMRrAAQoFQWxsb3cSSwoMQWN0aW9uRXF1YWxzEgZBY3Rpb24aMwoNb3RzOkJhdGNoR2V0KgoNb3RzOkRlc2NyaWJlKgoIb3RzOkdldCoKCW90czpMaXN0KhJqCg5SZXNvdXJjZUVxdWFscxIIUmVzb3VyY2UaTgohYWNzOm90czoqOio6aW5zdGFuY2UvcmFtLXRlc3QtYXBwCilhY3M6b3RzOio6KjppbnN0YW5jZS9yYW0tdGVzdC1hcHAvdGFibGUvKkoQMTk4MzQwNzU5Njk0NDIzN1IFMjY4NDJaD0Fzc3VtZWRSb2xlVXNlcmAAahIzOTYwMjU3NTI3NDY2MTQwNzhyEnJhbXRlc3RhcHByZWFkb25seQ==
        
        	OTS-TableStoreTest>: get test_write_read '001'
        	age:INTEGER='30'
        
        ```

-   Use temporary authorization to access the console

    In the previous example, RAM user ram\_test\_app can assume role RamTestAppReadOnly to obtain the permission to view all instances and tables. The logon process is as follows:

    1.  Log on to the[RAM console](https://partners-intl.console.aliyun.com/#/ram)using the primary account.
    2.  Log on to the RAM console using the primary account.
    3.  Click **RAM User Logon Link** and log on using RAM user ram\_test\_app.
    4.  Click the RAM user name and click **Switch Role**.
    5.  Enter the enterprise alias and role name, and click **Switch**.

## Use temporary authorization to call Java SDK { .section}

Create an OTSClient object, and pass in parameters of STS Token, including AccessKeyID, AccessKeySecret, and Token.

```
OTSClient client = new OTSClient(otsEndpoint, stsAccessKeyId, stsAccessKeySecret, instanceName, stsToken);

```

