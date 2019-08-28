# （可选）绑定VPC {#task_pc5_1yd_cfb .task}

VPC内的ECS实例可以通过VPC网络访问同地域的表格存储实例，访问前您需要先将VPC绑定到表格存储实例。

## 操作步骤 {#section_4hd_4zl_vqp .section}

1.  登录[表格存储控制台](https://ots.console.aliyun.com)。
2.  找到目标实例，单击其名称或右侧操作栏中的**管理**按钮，进入实例详情页面。
3.  单击**绑定VPC**，弹出绑定VPC窗口。
4.  选择目标VPC及交换机，根据提示自定义**VPC Name**，单击**确定**。 

    **说明：** 如果您用子账号登录管理 VPC，请确保已使用主账号在[访问控制RAM - 用户管理](https://ram.console.aliyun.com/?spm=a2c4g.11186623.2.7.67ed198fFi9zEl#/user/list)授予该子账号相关的 VPC 权限（AliyunVPCReadOnlyAccess），否则会因为没有权限而无法获取相关的 VPC 信息。


绑定成功后，在实例详情页面的**VPC 列表**栏中可查看已绑定的VPC信息。该VPC中的ECS实例可以通过实例详情中的VPC地址访问此表格储存实例。

## 解除绑定 {#section_g5s_yux_tvp .section}

您可以在VPC列表中单击**解除绑定**来删除实例和VPC的绑定关系。在解除绑定后，在该VPC内的ECS实例无法再通过VPC地址来访表格存储了，如仍需访问需再次绑定。

