# Table Store HBase Client {#concept_50125_zh .concept}

除了使用现有的 SDK 以及 Restful API 来访问表格存储，我们还提供了 Table Store HBase Client。使用开源 HBase API 的 JAVA 应用可以通过 Table Store HBase Client 来直接访问表格存储服务。Table Store HBase Client 基于表格存储 4.2.x 以上版本的 JAVA SDK，支持 1.x.x 版本以上的开源 HBase API。

Table Store HBase Client 可以从以下三个途径获取：

-   GitHub：[tablestore-hbase-client 项目](https://github.com/aliyun/aliyun-tablestore-hbase-client) 
-    [压缩包下载](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/50125/cn_zh/1486705375325/tablestore-hbase-client-1.2.0.zip) 
-   Maven

    ```language-xml
     <dependencies>
            <dependency>
                <groupId>com.aliyun.openservices</groupId>
                <artifactId>tablestore-hbase-client</artifactId>
                <version>1.2.0</version>
            </dependency>
        </dependencies>
    
    ```


由于表格存储是一个全托管的 NoSQL 数据库服务，当使用 Table Store HBase Client 之后，用户不再需要关心 HBase Server 的相关事项，只需要通过 Client 暴露出来的接口进行表或者数据的操作即可。

相比自行搭建 HBase 服务，表格存储有着如下的优势：

| |表格存储|自建HBase集群|
|:-|:---|:--------|
|成本|根据实际用量进行计费，提供高性能与容量型两种规格实例，适用于不同的应用场景。|需要根据业务峰值进行资源配置，空闲时段资源被闲置，租用及人工运维成本高。|
|安全|整合阿里云 RAM 资源权限管理系统，支持多种鉴权和授权机制及 VPC、主/子账号功能，授权粒度达到表级别和 API 级别。|需要额外的安全机制。|
|可靠性|数据自动多重冗余备份，故障迁移自动完成，可用性不低于 99.9%，数据可靠性达 99.99999999%。|需要自行保障集群的可用性。|
|可扩展性|表格存储的自动负载均衡机制支持单表 PB 级数据，即使百万并发也无需任何人工扩容。|集群利用率到一定水位之后需要繁琐的机器上下线流程，影响在线业务。|

