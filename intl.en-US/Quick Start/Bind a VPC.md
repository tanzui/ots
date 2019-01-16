# Bind a VPC {#task_pc5_1yd_cfb .task}

Virtual Private Cloud \(VPC\) is a logically isolated, private network environment built on Alibaba Cloud. Using VPC, you can select a private IP address range, divide network segments, and configure a routing table and gateway. VPC can also connect to a traditional data center through a leased line or a VPN to achieve a custom network environment for efficient cloud migration.

-   Create a VPC.

    When you create a VPC, select a region that makes sure your VPC and the Table Store instance can be located in the same region. For more information, see [Create a VPC](https://www.alibabacloud.com/help/doc-detail/27710.htm).

-   Create an ECS instance in the new VPC.

    After the VPC is created, create an ECS instance in the VPC . For more information, see [Create an ECS instance](https://www.alibabacloud.com/help/doc-detail/27713.htm).


1.  Log on to the [Table Store console](https://ots.console.aliyun.com). 
2.  Select the region where the created VPC is located, and click **Create Instance**. 
3.  Enter the required information and click **OK**. You are directed to the Instance List page.
4.  Locate and click the created instance, or click **Manage**in the **Actions** column to go to the Instance Details page. 
5.  Click **Bind VPC**. 
6.  Enter the required information and click **OK**. 

    **Note:** If you log on to the console using a RAM user, make sure that you have granted the RAM user VPC permission \(AliyunVPCReadOnlyAccess\) by using a primary account on the [RAM - Users console](https://ram.console.aliyun.com/?spm=a2c4g.11186623.2.7.67ed198fFi9zEl#/user/list). Otherwise, you cannot obtain VPC information.

    When the instance and VPC are bound successfully, you are directed to the Instance Details page automatically. You can click VPC List to view information about the bound VPC, or click the link in the **VPC ID** column to go to the VPC Instance Listpage and view the Table Store instance bound to the VPC, in addition to the VPC information list.

    To access Table Store from the ECS instance in the bound VPC, use the bound VPC access address as the endpoint for access.

    To delete the relationship between the Table Store instance and VPC, locate the bound VPC and click **Unbind**.

    Once unbound, the preceding address cannot be used to access Table Store from the ECS instance in the VPC. If you want to access Table Store again, rebind the Table Store instance to the VPC.


