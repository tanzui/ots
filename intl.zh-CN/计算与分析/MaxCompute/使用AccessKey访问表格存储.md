# 使用AccessKey访问表格存储 {#concept_64226_zh .concept}

除了授权方式外，您还可以在 MaxCompute 中使用 AccessKey 访问表格存储的数据。

## 准备工作 {#section_w1m_ljj_dfb .section}

获取表格存储资源所属账号的[AccessKeyId 和 AccessKeySecret](https://www.alibabacloud.com/help/faq-detail/63482.htm)，如果该 AK 是资源所属账号的子账号，那么该子账号至少需要对表格存储相关的资源具有以下权限：

```language-sql
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

--您也可以自定义其他权限

```

## 在 MaxCompute 中使用 AccessKey 访问表格存储 { .section}

同授权方式不同的是，需要在创建外表时在`LOCATION`中显示写入 AK 信息，其格式为：

```language-sql
LOCATION 'tablestore://${AccessKeyId}:${AccessKeySecret}@${InstanceName}.${Region}.ots-internal.aliyuncs.com'

```

假设需要访问的表格存储资源的信息为：

|AccessKeyId|AccessKeySecret|实例名称|区域|网络模式|
|:----------|:--------------|:---|:-|:---|
|abcd|1234|cap1|cn-hangzhou|内网访问|

创建外表的语句为：

```
CREATE EXTERNAL TABLE ads_log_ots_pt_external
(
vid bigint,
gt bigint,
longitude double,
latitude double,
distance double ,
speed double,
oil_consumption double
)
STORED BY 'com.aliyun.odps.TableStoreStorageHandler'
WITH SERDEPROPERTIES (
'tablestore.columns.mapping'=':vid, :gt, longitude, latitude, distance, speed, oil_consumption',
'tablestore.table.name'='vehicle_track'
)
LOCATION 'tablestore://abcd:1234@cap1.cn-hangzhou.ots-internal.aliyuncs.com'

```

对数据访问的操作步骤请参考[使用MaxCompute访问表格存储](intl.zh-CN/计算与分析/MaxCompute/使用MaxCompute访问表格存储.md#)中的步骤3.通过外部表访问 Table Store 数据。

