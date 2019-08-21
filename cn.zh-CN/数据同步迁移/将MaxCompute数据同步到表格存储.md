# 将MaxCompute数据同步到表格存储 {#concept_1340147 .task}

本文主要为您介绍如何在DataWorks控制台将MaxCompute中的全量数据同步到表格存储中。

## 背景 {#section_vfo_raq_ve8 .section}

表格存储能够支持千万TPS以及毫秒级延迟的服务能力，拥有强大的读写能力，同时，表格存储还提供多元索引等强大的索引功能，满足各种索引场景。您可以将MaxCompute计算分析后的数据同步到表格存储中，为您的应用提供更快的读写、搜索。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1068405/156635857852926_zh-CN.png)

## 步骤一 新增数据源 {#section_wio_jja_80o .section}

将表格存储数据库添加为新增数据源，具体操作如下：

1.  以项目管理员身份登录[DataWorks控制台](https://workbench.data.aliyun.com/console)。 

    **说明：** 仅项目管理员角色可以新建数据源，其他角色的成员仅可查看数据源。

2.  单击相应工作空间操作下的进入**数据集成**。
3.  选择**同步资源管理** \> **数据源**，单击**新增数据源**。
4.  在新增数据源弹出框中，选择数据源类型为**Tablestore**。
5.  在弹出的窗口中，填写配置。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1068405/156635857852793_zh-CN.png)

    |参数|说明|
    |--|--|
    |数据源名称|数据源的名称，例如，gps\_data。|
    |Endpoint|填入目标Tablestore实例的[服务地址](../../../../cn.zh-CN/开发指南/基础概念/服务地址.md#)。     -   如果Tablestore的实例和MaxCompute在同一个region，填入私网地址。
    -   如果Tablestore的实例和MaxCompute不在同一个region，填入公网地址。
    -   不能填入VPC地址。
 |
    |Tablestore实例ID|Tablestore的实例的名称。|
    |Access Id|登录账户的AccessKeyID。获取方式参见[创建AccessKey](../../../../cn.zh-CN/通用参考/创建AccessKey.md#)。|
    |Access Key|登录账户AccessKeyID对应的AccessKeySecret。获取方式参见[创建AccessKey](../../../../cn.zh-CN/通用参考/创建AccessKey.md#)。|

6.  单击**测试连通性**。
7.  单击**完成**，在**数据源**页面会出现该数据源信息。

## 步骤二 新建同步任务 {#section_2za_vy2_x4d .section}

创建并配置MaxCompute到表格存储的同步任务，具体操作如下：

1.  单击相应工作空间操作下的进入**数据开发**。
2.  在左侧导航栏，找到目标**业务流程**。 

    如需新建业务流程，参见[创建业务流程](https://help.aliyun.com/document_detail/85554.html?spm=5176.11065259.1996646101.searchclickresult.597578c3cFvSvX)。

3.  右键单击**数据集成**，选择**新建 数据集成 节点** \> **数据同步**，弹出**新建节点**窗口。每个同步任务都需创建一个相应的节点。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1068405/156635857852829_zh-CN.png)

4.  输入**节点名称**，单击**提交**。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1068405/156635857852836_zh-CN.png)

5.  在左侧导航栏中，单击**数据集成**，然后双击创建好的新节点。
6.  在同步任务页面，**数据来源**的**数据源**选择**ODPS**，然后选择对应的**表**。**数据去向**的**数据源**选择**OTS**。由于表格存储仅支持脚本模式，单击脚本图标，进行脚本配置。 

    具体脚本配置参见[配置Tablestore（OTS） Writer](https://help.aliyun.com/knowledge_detail/74480.html?spm=a2c4g.11186631.2.1.16b369a4Vu9FYh)。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1068405/156635857952841_zh-CN.png)

7.  单击保存图标。 

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1068405/156635857952857_zh-CN.png)

8.  单击页面上方的运行图标，开始执行任务。运行结束后，在日志中可以查看任务是否成功和导出的数据行数。

## 步骤三 配置调度参数 {#section_d18_1am_fix .section}

您可以通过调度配置页面，配置同步任务的执行时间等。

创建同步节点后，双击进入节点，并在页面右侧单击**调度配置**，进入配置页面。具体配置，参见[调度配置](https://help.aliyun.com/document_detail/85556.html?spm=a2c4g.11186623.2.31.1f764adcdSL5FM#title-00f-yeb-atk)。

## 步骤四 提交任务 {#section_e1a_8fj_idl .section}

单击顶部菜单栏中的提交图标，将同步任务提交至调度系统中。调度系统会根据配置的属性，自动定时执行。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1068405/156635857952879_zh-CN.png)

## 步骤五 查看任务 {#section_9n2_87c_v2g .section}

进入**运维中心**可查看新建的任务详情。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1068405/156635857952888_zh-CN.png)

