# STS临时授权访问 {#concept_27364_zh .concept}

STS 可以指定复杂的策略来对特定的用户进行限制，仅提供最小的权限。

当开发者的 APP 被用户使用之后，用户的数据要写入 ram-test-dev 这个实例。当 APP 的用户数据很多时，要求能够安全地授权给众多的 APP 用户上传数据，并且保证多个用户之间存储的隔离。这种场景需要临时访问权限，建议使用 STS 来完成。

## 创建角色 {#section_zzy_2ms_cfb .section}

1.  创建一个名为 ram\_test\_app 的子账号，不需要赋予任何权限，因为在扮演角色的时候会自动获得被扮演角色的所有权限。有关创建RAM用户更多详情，请参见使用示例中的步骤1至步骤7。
2.  创建两个角色，RamTestAppReadOnly 和 RamTestAppWrite。一个用于读取等操作，一个用于上传文件的操作。
    1.  登录[RAM 控制台](https://ram.console.aliyun.com)。
    2.  选择**角色管理** \> **新建角色**。
    3.  选择角色类型。这里选择**用户角色**。
    4.  填写类型信息。因为角色是被阿里云账号使用过的，因此选择默认的即可。然后单击**下一步**。
    5.  配置角色基本信息。本实例中角色名称填写 RamTestAppReadOnly，然后单击**创建**。
    6.  完成角色创建后，单击**关闭。** 
3.  创建完角色之后，角色是没有任何权限的，因此需要新建一个自定义的授权策略。
    1.  选择**策略管理** \> **新建授权策略**。
    2.  选择**空白模板**。
    3.  填写**授权策略名称**。该示例中填写ram-test-app-readonly，策略内容填写如下：

        ``` {#codeblock_9p6_nid_noe}
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

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20297/156144984012253_zh-CN.png)

        该策略内容表示对 ram-test-app 授予只读权限。

    4.  单击**创建授权策略**，然后单击**关闭**。
4.  为 RamTestAppReadOnly 添加上 ram-test-app 的只读授权。
    1.  在角色管理页面，单击 RamTestAppReadOnly 右侧操作栏中的**授权**按钮。
    2.  将 ram-test-app-readonly 权限添加至右侧栏中。完成给该角色赋予对 ram-test-app 拥有只读的权限。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20297/156144984111784_zh-CN.png)

    3.  单击**确定**。

        表示已为 RamTestAppReadOnly 角色授予了 ram-test-app 的只读权限。

5.  参考步骤 2 ~ 步骤 4，建立一个 RamTestAppWrite 的角色，并赋予该角色写 ram-test-app 的自定义授权，步骤 3 中填写的策略内容如下：

    ``` {#codeblock_1ud_jme_kax}
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


在角色管理页面，可以看到已经新建好了 RamTestAppReadOnly 和 RamTestAppWrite 两个角色，分别表示了对于 ram-test-app 的读和写权限。

## 临时授权访问 {#section_jmr_0bc_se4 .section}

创建角色后，就可以使用临时授权来访问表格存储了。

-   准备工作

    在使用 STS 来授权访问前，需要先对子账号进行需扮演角色的授权。若任意子账号都可以扮演这些角色，会带来不可预估的风险，因此有扮演对应角色需求的子账号需要被赋予相应的配置权限。在授权管理策略中新建两个自定义的授权策略并将其赋予 ram\_test\_app 这个子账号，操作步骤如下：

    1.  单击页面左侧的**策略管理**，进入策略管理页面。
    2.  单击**新建授权策略**按钮，进入创建授权策略的页面。
    3.  选择**空白模板**，进入创建自定义授权策略的页面。
    4.  填写授权策略名称，并将如下内容填写至策略内容栏。

        -   AliyunSTSAssumeRolePolicy2016011401
        ``` {#codeblock_9gr_96n_sy6}
        {
        "Version": "1",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": "sts:AssumeRole",
                "Resource": "acs:ram:198***237:role/ramtestappreadonly"
            }
        ]
        }
        							
        ```

        -   AliyunSTSAssumeRolePolicy2016011402
        ``` {#codeblock_n55_uhp_mts}
        {
        "Version": "1",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": "sts:AssumeRole",
                "Resource": "acs:ram:198***237:role/ramtestappwrite"
            }
        ]
        }
        							
        ```

        **说明：** 下文中 Resource 后面填写的内容表示某个角色 ID，角色的 ID 可以在角色管理页面，单击**管理**按钮，进入角色详情的页面中找到。

    5.  上述的两个授权策略都建成后，单击页面左侧的**用户管理**，进入用户管理页面。
    6.  单击 ram\_test\_app 右侧操作栏中的**授权**按钮。进入编辑个人授权策略页面。
    7.  搜索AliyunSTSAssumeRolePolicy2016011401和AliyunSTSAssumeRolePolicy2016011402，选中后单击 \> 将该权限添加至右侧栏中。
    8.  单击**确定**，完成将这两个授权赋给 ram\_test\_app 这个账号的操作。
-   使用 STS

    上述准备工作完成后，就可以正式使用 STS 来进行授权访问。这里需要使用 STS 的 Python 命令行工具，该工具安装包的下载地址：[sts.py](https://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/cn/oss/0.4.30/assets/tool/sts.py)。

    具体的调用方法如下，更详细的参数解释可以参考[STS 帮助文档](https://help.aliyun.com/document_detail/28756.html)。

    ``` {#codeblock_bny_y08_rnc}
    $python ./sts.py AssumeRole RoleArn=acs:ram::198***237:role/ramtestappreadonly RoleSessionName=usr001 Policy='{"Version":"1","Statement":[{"Effect":"Allow","Action":["ots:ListTable","ots:DescribeTable"],"Resource":["acs:ots:*:*:ram-test-app","acs:ots:*:*:ram-test-app/*"]}]}' DurationSeconds=1000 --id=id --secret=secret
    					
    ```

    参数说明：

    |参数|说明|
    |:-|:-|
    |RoleArn|指需要扮演的角色 ID。角色的 ID 可以在角色管理页面，单击**管理**按钮进入角色详情的页面中找到。|
    |RoleSessionName|指临时凭证的名称，一般来说建议使用不同的应用程序用户来区分。|
    |Policy|指在扮演角色时额外加上的一个权限限制。 **说明：** 这里传入的 Policy 是用来限制扮演角色之后的临时凭证的权限。最后临时凭证获得的权限是角色的权限和这里传入的 Policy 的交集。在扮演角色时传入 Policy 是为了灵活性，比如上传文件时可以根据不同的用户添加对于上传文件路径的限制。

 |
    |DurationSeconds|指临时凭证的有效期。单位是秒，最小为 900，最大为 3600。|
    |id and secret|指需要扮演角色的子账号的 AccessKey 的 AccessKeyID 和 AccessKeySecret。|

-   测试 STS 的作用

    先在[表格存储控制台](https://ots.console.aliyun.com)创建名称为 test\_write\_read 的表，主键为 name，类型为 string，然后使用 CLI 工具测试读/写操作。

    使用 ram\_test\_app 这个子账号直接来访问，请将下面的 AccessKey 换成自己测试用的 AccessKey。

    ``` {#codeblock_2xa_1mh_v0i}
    python2.7 ots_console --url https://TableStoreTest.cn-hangzhou.ots.aliyuncs.com --id <yourAccessKeyId> --key <yourAccessKeySecret>
    You cannot access the instance!
    ErrorCode: OTSNoPermissionAccess
    ErrorMessage: You have no permission to access the requested resource, please contact the resource owner.
    					
    ```

    由于 ram\_test\_app 这个子账号没有访问权限，因此访问失败。

-   使用临时授权写入数据

    使用 STS 来写入数据。这里为了简单，传入的 Policy 和角色的 Policy 一致，过期时间使用默认的 3600s，SessionName 假定为 session001。操作步骤如下：

    1.  使用 STS 来获取临时凭证。

        ``` {#codeblock_ckg_d62_1ap}
        python2.7 ./sts.py AssumeRole RoleArn=acs:ram::198***237:role/ramtestappwrite RoleSessionName=session001 Policy='{"Statement": [{"Effect": "Allow","Action": ["ots:Create*","ots:BatchWrite*","ots:Put*","ots:Insert*","ots:Update*","ots:Delete*"],"Resource": ["acs:ots:*:*:instance/ram-test-app","acs:ots:*:*:instance/ram-test-app/table/*"]}],"Version": "1"}' --id=<yourAccessKeyId> --secret=<yourAccessKeySecret>
        {
        "AssumedRoleUser": {
            "Arn": "acs:ram::198***237:role/ramtestappwrite/session001", 
            "AssumedRoleId": "33062905274959****:session001"
        }, 
        "Credentials": {
            "AccessKeyId": "***", 
            "AccessKeySecret": "***"
            "SecurityToken": "CAE****0ZQ=="
        }, 
        "RequestId": "5F92B248-F200-40F8-A05A-C9C7D018E351"
        }
        							
        ```

    2.  使用 CLI 工具写入数据（版本 V1.2 开始支持 token 参数，待发布）。

        ``` {#codeblock_sty_nnw_u05}
            python2.7 ots_console --url https://TableStoreTest.cn-hangzhou.ots.aliyuncs.com --id <yourAccessKeyId> --key <yourAccessKeySecret> --token=CAE****0ZQ==
        
            OTS-TableStoreTest>$ put test_write_read '001' age:integer=30
            A new row has been put in table test_write_read
        							
        ```

-   使用临时授权读取数据

    使用 STS 来读取数据。这里为了简单，传入的 Policy 和角色的 Policy 一致，过期时间使用默认的 3600s，SessionName 假定为 session002。操作步骤如下：

    1.  使用 STS 来获取临时凭证。

        ``` {#codeblock_uc7_fmc_v54}
        python2.7 ./sts.py AssumeRole RoleArn=acs:ram::198***237:role/ramtestappreadonly RoleSessionName=session002 Policy='{"Statement": [{"Effect": "Allow","Action": ["ots:BatchGet*","ots:Describe*","ots:Get*","ots:List*"],"Resource": ["acs:ots:*:*:instance/ram-test-app","acs:ots:*:*:instance/ram-test-app/table/*"]}],"Version": "1"}' --id=6iT***lRt --secret=****
        {
        "AssumedRoleUser": {
            "Arn": "acs:ram::198***237:role/ramtestappreadonly/session002",
            "AssumedRoleId": "396025752746614078:session002"
        },
        "Credentials": {
            "AccessKeyId": "***",
            "AccessKeySecret": "***",
            "Expiration": "2016-01-14T08:14:16Z",
            "SecurityToken": "CAE****seQ=="
        }, 
        "RequestId": "EE788165-B760-4014-952C-E58ED229C80D"
        }
        							
        ```

    2.  使用 CLI 工具读取数据（版本 V1.2 开始支持 token 参数，待发布）。

        ``` {#codeblock_mzy_hmj_gga}
            python2.7 ots_console --url https://TableStoreTest.cn-hangzhou.ots.aliyuncs.com --id STS***Q8Q --key **** --token=CAE****Q==
        
            OTS-TableStoreTest>: get test_write_read '001'
            age:INTEGER='30'
        							
        ```

-   使用临时授权访问控制台

    STS 临时授权允许子账户登录表格存储控制台，并管理和查看主账号的实例和表资源。上面的例子中，子账号 ram\_test\_app 可以扮演 RamTestAppReadOnly 角色，从而拥有查看所有实例和所有表的权限。登录步骤如下：

    1.  使用主账号登录[访问控制RAM控制台](https://ram.console.aliyun.com)。
    2.  使用主账号登录访问控制RAM控制台，进入概览页面。
    3.  单击**RAM 用户登录链接**后面的链接，进入阿里云 RAM 用户登录页面，输入子账号的 ram\_test\_app 用户名及密码进行登录。
    4.  登录成功后，单击页面右上角的用户名，然后单击**切换身份**，进入角色切换页面。
    5.  输入企业姓名和角色名，然后单击**切换**。

## 使用临时授权调用 JAVA SDK {#section_fgm_81g_owv .section}

请参考以下方式创建 OTSClient 对象，传入 STS Token 的 AccessKeyId、AccessKeySecret 和 Token 等参数。

``` {#codeblock_692_9hu_529}
OTSClient client = new OTSClient(otsEndpoint, stsAccessKeyId, stsAccessKeySecret, instanceName, stsToken);
			
```

## 总结 {#section_nmt_ddt_3qq .section}

本章主要介绍了如何使用 STS 来临时授权用户访问表格存储。在典型的移动开发场景中，通过使用 STS，不同的 app 用户需要访问 app 时，可以通过获取到的临时授权来访问表格存储，临时授权可以指定过期时间，因此大大降低了泄露子账号信息的危害。在获取临时授权的时候，可以根据 app 用户的不同，传入不同的授权策略来限制用户的访问权限，比如限制用户访问的表路径，从而达到隔离不同 app 用户的存储空间的目的。

