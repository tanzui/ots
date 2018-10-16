# Allow MaxCompute to access Table Store using AccessKeys {#concept_64226_zh .concept}

In addition to account authorization, you can access data in Table Store using AccessKeys on MaxCompute.

## Preparation {#section_w1m_ljj_dfb .section}

Get the AccessKey credentials \(that is, [AccessKeyId and AccessKeySecret](https://partners-intl.aliyun.com/help/faq-detail/63482.htm)\) for the account that owns the target Table Store resources. If the AccessKey is for a Resource Access Management \(RAM\) user, the RAM user must be granted with at least the following permissions to operate the Table Store resources:

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

-- You can also define other permissions

```

## Allow MaxCompute to access Table Store using AccessKeys { .section}

Unlike account authorization, you must specify the AccessKey information in the `LOCATION` clause when creating an external table.

```language-sql
LOCATION 'tablestore://${AccessKeyId}:${AccessKeySecret}@${InstanceName}. ${Region}.ots-internal.aliyuncs.com'

```

Assume that the following information is what MaxCompute must access:

|AccessKeyId|AccessKeySecret|Instance name|Region|Network mode|
|:----------|:--------------|:------------|:-----|:-----------|
|abcd|1234|cap1|cn-hangzhou|Intranet access|

The statement to create the external table is:

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

For more information about accessing data, see the following section in [Allow MaxCompute to access Table Store using one account](reseller.en-US/Compute-Analysis/MaxCompute /Allow MaxCompute to access Table Store using one account.md#): Step 3. Access Table Store data through external tables

