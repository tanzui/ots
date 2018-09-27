# 增量同步（DataX） {#concept_62149_zh .concept}

本章介绍如何将Table Store中的增量数据通过[开源工具DataX](https://github.com/alibaba/DataX) 同步到Elasticsearch中。

DataX是阿里巴巴集团内广泛使用的离线数据同步工具/平台，实现包括MySQL、Oracle、HDFS、Hive、Table Store、MaxCompute等各种异构数据源之间高效的数据同步功能。

## 途径 {#section_dt2_1lb_dfb .section}

DataX

-   Reader：OTSStreamReader
-   Writer：ElasticsearchWriter

## 限制 { .section}

-   Table Store数据变化类型

    -   仅支持PUT（新增），UPDATE（更新）两种操作。
    -   不支持DELETE操作。
-   增量同步任务延时

    -   Table Store Stream是实时增量通道，但是调度采用数据集成，最小调度时间是5分钟。并且，Table Store Stream Reader的DataX插件限制了最快只能处理5分钟前的数据，所以目前同步任务会有5~10分钟的延迟。

## 配置Table Store { .section}

无须配置。

## 配置Elasticsearch { .section}

无须配置。

## 配置DataX { .section}

1.  登录VPC环境内的ECS，确定一个工作目录。
2.  准备配置文件。

    |DataX主要配置项|描述|
    |:---------|:-|
    |job的控制参数|-|
    |Reader的参数|Reader配置otsstreamreader，包括name和parameter参数。parameter参数中包含了Reader所需的自定义参数。|
    |Writer的参数|Writer配置elasticsearchwriter，包括name和parameter参数。parameter参数中包含了Writer所需的自定义参数。|

    配置文件的格式如下所示。下述配置表示：

    -   同步Table Store实例your-instance中表table\_name的三列（主键列：uid，属性列：name，phone）的更新值（新增，更新，删除）到Elasticsearch中。

    -   同步时间戳：1510036262000（2017/11/07 14:31:21）到 1510036881000（2017/11/07 14:41:21）之间发生变化的数据。

        ```
        {
        "job": {
          "setting": {
              "speed": {
                  "channel": 1 # 通道数目，默认是1，可以根据自己数据量大小填写
              },
              "errorLimit": {
                  "percentage": 0 # 允许的错误比例，一般设置为0即可
              }
          },
          "content": [
              {
                  "reader": {
                      "name": "otsstreamreader",  # 读取插件名称：OTSStreamReader
                      "parameter": {
                          "endpoint": "https://your-instance.cn-hangzhou.ots.aliyuncs.com", # Table Store的实例名称
                          "accessId": "OyR5xxxxxxxxXaXi",  # 阿里云AccessKeyID
                          "accessKey": "Z3wVxxxxxxxxxxxxxxxxxxxxxxxxRZ",  # 阿里云AccessKeySecret
                          "instanceName": "your-instance.",  # Table Store的实例名称
                          "dataTable": "table_name",  # Table Store中需要同步到Elasticsearch的表名
                          "statusTable": "TableStoreStreamReaderStatusTable", # 存储TableStore Stream状态的表，一般不需要修改
                          "startTimestampMillis": 1510036262000,  # 开始导出的时间点
                          "endTimestampMillis": 1510036881000,  # 结束导出的时间点
                          "mode": "single_version_and_update_only", # Table Store Stream导出数据的格式，目前Elasticsearch只能接收这种格式的，这个不需要修改
                          "column":[  # 需要导出Table Store中的哪些列到Elasticsearch中去
                              {"name":"uid"},
                              {"name":"name"},
                              {"name":"phone"}
                          ],
                          "isExportSequenceInfo": false, # single_version_and_update_only 模式下只能是false
                          "maxRetries": 30 # 最大重试次数
                      }
                  },
                  "writer": {
                      "name": "elasticsearchwriter", # Reader插件的名称：ElasticsearchWriter，不需要修改
                      "parameter": {
                          "accessId": "OyR5xxxxxxxxXaXi",   # AccessKeyID，如果不需要则填空值就行，阿里云Elasticsearch需要，无安全验证的开源Elasticsearch则不需要
                          "accessKey": "Z3wVxxxxxxxxxxxxxxxxxxxxxxxxRZ",  # AccessKeySecret，如果不需要则填空值就行，阿里云Elasticsearch需要，无安全验证的开源Elasticsearch则不需要
                          "endpoint": "https://127.0.0.1:9200",  # Elasticsearch的endpoint
                          "index": "school_index", # Elasticsearch的索引名称，比如这里的索引名称是school_index
                          "type": "user_info",  # Elasticsearch中相应索引下的类型名称，这里是user_info
                          "cleanup": false,  # 是否在每次导入数据到Elasticsearch的时候清空原有数据，全量导入/重建索引的时候需要设置为true，同步增量的时候必须为false
                          "discovery":true, # 是否自动发信，设置为true
                          "batchSize": 100,  # 每批导出的格式
                          "column": [   # Elasticsearch中的列名，顺序和Reader中的Column顺序一致
                              {
                                  "name": "uid",  # Table Store中的主键列是uid，这里也有同名uid，用type：id表示这一列是主键
                                  "type": "id"  # id表示这一列是主键，id不是Elasticsearch的内置类型，是ElasticsearchWriter提供的虚拟类型
                              },
                              {
                                  "name": "name", # 对应于Table Store中的属性列：name
                                  "type": "text"  # 文本类型，采用默认分词
                              },
                              {
                                  "name": "phone", # 对应于Table Store中的属性列：phone
                                  "type": "text"  # 文本类型，采用默认分词
                              }
                          ]
                      }
                  }
              }
          ]
        }
        }
        ```

3.  完成配置文件后，执行DataX命令。

    ```
    python /home/admin/datax3/bin/datax.py -j"-Xms4g -Xmx4g" /path/to/job_config.json
    ```

4.  检查数据是否已经导入到Elasticsearch中。

    通过如下命令查看导入Elasticsearch中的文档数：

    ```
    curl -XGET https://endpoint/index_name/type_name/_count?pretty  -d '
    {
     "query": {
         "match_all": {}
     }
    }'
    ```

    返回结果类似如下所示：

    ```
    {
    "count" : 1000,  # Elasticsearch中index_name索引的type_name类型中的doc数
    "_shards" : {     # 这个是Elasticsearch返回数据相关的meta值，表示总共有5个shard，全部成功返回了结果
     "total" : 5,
     "successful" : 5,
     "skipped" : 0,
     "failed" : 0
    }
    }
    ```

    如果以上数据准确，则可以配置定时导出任务。

5.  配置定时导出任务。

    因为使用了DataX，您需要自己通过脚本来控制调度。简单的控制流程如下所示：

    1.  构造一个配置文件模板，配置文件模板中的startTimestampMillis和endTimestampMillis设置为两个变量，例如，\{start\_time\}和\{end\_time\}。
    2.  实现一个脚本：run\_datax.py
        -   每隔5分钟执行一次。
        -   执行完成后记录本次执行的起始时间last\_time。
        -   开始执行的时候获取上次执行的起始时间，然后设置start\_time=last\_time+ 5分钟，end\_time= last\_time+10分钟。
        -   通过命令`python /home/admin/datax3/bin/datax.py -j"-Xms4g -Xmx4g" -D start_time =xxxxx -D end_time =yyyyy /path/to/job_config`启动一次DataX同步任务。

