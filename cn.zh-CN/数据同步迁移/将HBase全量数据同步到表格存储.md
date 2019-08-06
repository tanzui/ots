# 将HBase全量数据同步到表格存储 {#task_1546583 .task}

本文指导您如何使用DataX将HBase数据库中的全量数据同步迁移到表格存储（Table Store）中。

## 准备工作 {#section_1j3_gdb_250 .section}

-   [开通表格存储服务](../../../../cn.zh-CN/快速入门/开通表格存储服务.md#)
-   创建表格存储实例

    您需要在表格存储控制台[创建实例](../../../../cn.zh-CN/快速入门/创建实例.md#)，然后在实例详情页面获取实例的服务地址（Endpoint）。

-   创建表格存储数据表

    您需要创建用于存放迁移数据的表格存储数据表，具体参见[创建数据表](../../../../cn.zh-CN/快速入门/创建数据表.md#)。

    **说明：** 创建数据表时建议使用HBase原主键或唯一索引作为表格存储数据表的主键。

-   获取AccessKey

    要接入表格存储服务，您需要拥有一个有效的AccessKey（包括AccessKeyId和AccessKeySecret）用来进行签名认证。具体参见[创建AccessKey](../../../../cn.zh-CN/通用参考/创建AccessKey.md#)。


## 步骤一：下载DataX {#section_6s6_ph6_1pt .section}

[DataX](https://github.com/alibaba/DataX/blob/master/introduction.md)​是一个异构数据源离线同步工具，本案例中使用DataX将HBase中的数据同步到表格存储。

您可以选择下载DataX的源代码（开源）进行本地编译，也可以直接下载编译好的压缩包。

-   下载DataX的源代码
    -   GitHub下载

        安装Git工具，然后执行以下操作：

        ``` {#codeblock_1v2_z46_ud1}
        git clone https://github.com/alibaba/DataX.git
        ```

    -   [本地下载](http://datax-opensource.oss-cn-hangzhou.aliyuncs.com/datax.tar.gz)
-   下载编译好的压缩包
    -   DataX的GitHub地址上获得下载链接：

        ``` {#codeblock_qwo_3e8_g7i}
        https://github.com/alibaba/DataX
        ```

    -   [本地下载](http://datax-opensource.oss-cn-hangzhou.aliyuncs.com/datax.tar.gz?spm=a2c4e.11153940.0.0.318c29fbVnjt3M&file=datax.tar.gz)

## 步骤二：Maven打包 {#section_u5c_qlf_dfz .section}

具体操作步骤如下：

**说明：** 

-   如果您下载的是编译好的DataX压缩包，跳过此步。
-   此步骤会在本地编译各种数据源的Writer和Reader，会花费较长的时间，需要耐心等待。

1.  进入到下载的源码目录，然后执行：

    ``` {#codeblock_ynf_eva_had}
    mvn -U clean package assembly:assembly -Dmaven.test.skip=true
    ```

2.  编译完成后， 进入/target/datax/datax目录，其中：

    |目录|说明|
    |--|--|
    |bin|存放着可执行的datax.py文件，是整个DataX工具的入口。|
    |plugin|存放支持各种类型数据源的Reader和Writer。|
    |conf|存放core.json文件，文件中定义了一些缺省参数值如channle流控、buffer大小等参数，一般不随意修改。|


## 步骤三：准备全量导出的JSON文件 {#section_vaz_s92_1ik .section}

DataX提供了HbaseReader插件从HBase中读取数据。在底层实现上，HbaseReader通过HBase的Java客户端连接远程HBase服务，并通过Scan方式读取您指定rowkey范围内的数据，并将读取的数据使用DataX自定义的数据类型拼装为抽象的数据集，并传递给下游Writer处理。

您可以根据HBase版本选择相应的Reader插件导出JSON文件：

-   [Hbase0.94 XReader](https://github.com/alibaba/DataX/blob/master/hbase094xreader/doc/hbase094xreader.md)
-   [Hbase1.1 XReader](https://github.com/alibaba/DataX/blob/master/hbase11xreader/doc/hbase11xreader.md)
-   [Hbase2.0 XReader](https://github.com/alibaba/DataX/blob/master/hbase20xsqlreader/doc/hbase20xsqlreader.md) 

    Hbase2.0XReader插件从Phoenix读取数据。


**说明：** DataX仅提供HBase0.94、HBase1.1以及HBase2.0的导出插件，如需导出其他版本的HBase数据， 请参考HBase API实现导出工具。

 **配置样例** 

使用Hbase1.1 XReader配置一个从 HBase 抽取数据到本地的作业:（normal 模式）：

``` {#codeblock_mwl_gga_e89}
{
    "job": {
        "setting": {
            "speed": {
                "channel": 1
            }
        },
        "content": [
            {
                "reader": {
                    "name": "hbase11xreader",
                    "parameter": {
                        "hbaseConfig": {
                            "hbase.zookeeper.quorum": "xxxf"
                        },
                        "table": "users",
                        "encoding": "utf-8",
                        "mode": "normal",
                        "column": [
                            {
                                "name": "rowkey",
                                "type": "string"
                            },
                            {
                                "name": "info: age",
                                "type": "string"
                            },
                            {
                                "name": "info: birthday",
                                "type": "date",
                                "format":"yyyy-MM-dd"
                            },
                            {
                                "name": "info: company",
                                "type": "string"
                            },
                            {
                                "name": "address: contry",
                                "type": "string"
                            },
                            {
                                "name": "address: province",
                                "type": "string"
                            },
                            {
                                "name": "address: city",
                                "type": "string"
                            }
                        ],
                        "range": {
                            "startRowkey": "",
                            "endRowkey": "",
                            "isBinaryRowkey": true
                        }
                    }
                },
                "writer": {
                    "name": "txtfilewriter",
                    "parameter": {
                        "path": "/Users/shf/workplace/datax_test/hbase11xreader/result",
                        "fileName": "qiran",
                        "writeMode": "truncate"
                    }
                }
            }
        ]
    }
}
```

## 步骤四：执行同步命令 {#section_y11_s93_g9p .section}

``` {#codeblock_mvj_ona_7ou}
python datax.py  -j"-Xms4g -Xmx4g" hbase_to_ots.json
```

`-j"-Xms4g -Xmx4g"` 可以限制占用JVM内存的大小。如果不指定，将会使用`conf/core.json`中的配置，默认是1GB。

## 相关操作 {#section_d5b_dfm_c7d .section}

HBase数据迁移到表格存储后，您可以使用Table Store SDK 或者Table Store HBase Client读取表格存储的数据，具体参见[从 HBase 迁移到表格存储](../../../../cn.zh-CN/开发指南/HBase 支持/从 HBase 迁移到表格存储.md#)。

