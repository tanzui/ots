# 全量导出（DataX） {#concept_62161_zh .concept}

本章介绍如何将Table Store中的全量数据通过[开源工具DataX](https://github.com/alibaba/DataX) 导出到Elasticsearch中。

DataX是阿里巴巴集团内广泛使用的离线数据同步工具/平台，实现包括MySQL、Oracle、HDFS、Hive、Table Store、MaxCompute等各种异构数据源之间高效的数据同步功能。

## 途径 {#section_gx5_3l1_dfb .section}

DataX

-   Reader：OTSSReader
-   Writer：ElasticsearchWriter

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

    -   导出Table Store实例your-instance中表table\_name的所有行的三列（主键列：uid，属性列：name，phone\_number）数据到Elasticsearch中。

    -   同步uid数，同步范围：从INF\_MIN到INF\_MAX，即导出所有主键的数据。

    ```language-json
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
                       "name": "ots",  # 不能修改
                       "parameter": {
                           "datasource": "",   # 数据集成中的数据源名称，需要提前设置好，这里有两种选择，一种是配置datasource数据源，一种是写明文的AccessKeyID等鉴权信息。建议使用数据源。如果配置为写明文方式，则需要新增四个配置项（"endpoint"，"accessId":""，"accessKey":""，"instanceName"）替换掉datasource。
                           "table": "",    # Table Store中的表名
                           "column": [   # 需要导出到Elasticsearch中的列名
                             {
                                "name": "uid"    # Table Store中列名，此列需要导入到Elasticsearch
                             },
                             {
                                "name": "name"   # Table Store中列名，此列需要导入到Elasticsearch
                             },
                             {
                                 "name": "phone_number"   # Table Store中列名，此列需要导入到Elasticsearch
                             }
                             ],
                             "range": {
                                "begin": [
                                {
                                    "type": "INF_MIN"   # Table Store中第一列主键的起始位置。如果要导出全量，这里需要配置为INF_MIN，如何导出部分，则按需要配置。如果有几列主键列，这里begin中就需要几项配置，但是当导出目标为Elasticsearch的时候仅支持单列主键，所以这里仅演示单列的配置
                                }
                                ],
                                "end": [
                                {
                                   "type": "INF_MAX"   # Table Store中第一列主键的结束位置。如果要导出全量，这里需要配置为INF_MAX，如何导出部分，则按需要配置。
                                }
                                ],
                             "split": [  # 用来配置Table Store的表的分区信息，可以加速导出，下一个版本会自动处理。
                              ]
                            }
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
                                   "name": "phone_number", # 对应于Table Store中的属性列：phone
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
    # 把命令中的${index_name}替换成你自己的index_name，把${type_name}替换成你自己的type_name。
     curl -XGET https://endpoint/${index_name}/${type_name}/_count?pretty  -d '
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

    从Table Store导出全量数据到Elasticsearch中的场景，一般是一次性的，不需要配置定时任务。

    如果您需要每隔一段时间重建索引，则可以配置crontab，定时执行导出任务的命令。


