# Java SDK 历史迭代版本 {#concept_27351_zh .concept}

4.0.0 以上版本的 SDK 支持数据多版本和生命周期，但是该版本 SDK 不兼容 2.x.x 系列的 SDK。

## 版本号：4.11.0 {#section_ls4_d2c_6f7 .section}

发布时间：2019-04-12

下载地址：[4.11.0](https://oss.sonatype.org/service/local/artifact/maven/redirect?r=releases&g=com.aliyun.openservices&a=tablestore&v=4.11.0&e=zip&c=release)

更新日志：新增Timestream模型

## 版本号：4.10.2 {#section_kzf_x1c_bhb .section}

发布时间：2019-03-11

下载地址：[tablestore-4.10.2](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/108096/cn_zh/1552274752739/tablestore-4.10.2-release.zip)

更新日志：

-   Remove log4j2 implement and log4j2.xml.
-   TunnelWorker: fix auto retry logic when network condition is too bad.

## 版本号：4.10.0 {#section_phw_ldk_wgb .section}

发布时间：2019-02-20

下载地址：[tablestore-4.10.0](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/108096/cn_zh/1550713343909/tablestore-4.10.0-release.zip)

更新日志：新增通道服务功能

-   新增通道服务管控接口
-   新增全增量一体化数据处理框架

## 版本号：4.8.0 {#section_ods_3kq_dgb .section}

发布时间：2018-12-17

下载地址：[tablestore-4.8.0-release.zip](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/97272/cn_zh/1545014781181/tablestore-4.8.0-release.zip)

更新日志：

SearchIndex相关

-   支持设置IndexSort。
-   使用Token替换SearchAfter进行翻页。
-   新增TermsQuery。
-   支持设置TotalCount是否返回\(默认不返回\)。

日志配置和线程名调整。

## 版本号：4.7.4 {#section_i1n_wlt_4fb .section}

发布时间：2018-09-27

下载地址：[tablestore-4.7.4-release.zip](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/86690/cn_zh/1540433142084/tablestore-4.7.4-release.zip)

更新日志：

-   新增SearchIndex功能：
    -   多字段检索
    -   范围查询
    -   通配符查询
    -   嵌套查询
    -   全文检索
    -   排序
-   新增全局二级索引。

## 版本号：4.3.1 {#section_rpl_q4k_2fb .section}

发布时间：2017-04-27

下载地址：[tablestore-4.3.1-release.zip](https://repo.maven.apache.org/maven2/com/aliyun/openservices/tablestore/4.3.1/tablestore-4.3.1-release.zip)

更新日志：提供按照指定大小将全表数据逻辑分片的功能。

## 版本号：4.2.1 { .section}

发布时间：2017-01-18

下载地址：[aliyun\_tablestore\_java\_sdk\_4.2.1.zip](https://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/43005/cn_zh/1484715297472/tablestore-4.2.1.zip)

更新日志：修复PrimaryKeyValue和PrimaryKeySchema无法放入HashMap的问题。

## 版本号：4.2.0 { .section}

发布时间：2016-11-29

下载地址：[aliyun\_tablestore\_java\_sdk\_4.2.0.zip](https://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/43005/cn_zh/1480417507212/tablestore-4.2.0.zip)

更新日志：增加主键列自增功能。

## 版本号：4.1.0 {#section_xkz_4mq_dgb .section}

发布时间：2016-10-11

下载地址：[aliyun\_tablestore\_java\_sdk\_4.1.0.zip](https://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/43005/cn_zh/1476171171854/aliyun_tablestore_java_sdk_4.1.0.zip)

更新日志：DescribeTable 接口返回分片间的分隔点，可用于确定分片范围。

## 版本号：4.0.0 { .section}

发布时间：2016-08-01

下载地址：[aliyun\_tablestore\_java\_sdk\_4.0.0.zip](https://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/43005/cn_zh/1470024227102/aliyun_tablestore_java_sdk_4.0.0.zip)

更新日志：

-   新增[数据生命周期 TTL](../../../../../cn.zh-CN/数据模型/Wide Column/数据版本和生命周期.md#section_cv2_kyf_cfb)。
-   新增[数据多版本](../../../../../cn.zh-CN/数据模型/Wide Column/数据版本和生命周期.md#)。

## 版本号：2.2.5 { .section}

发布时间：2016-08-23

下载地址：[aliyun\_tablestore\_java\_sdk\_2.2.5.zip](https://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/27351/cn_zh/1471922334342/ots-public-2.2.5-release.zip)

更新日志：解决 OTSWriter 中可能导致程序 Hang 的一个 bug。

## 版本号：2.2.4 { .section}

发布时间：2016-05-12

下载地址：[aliyun\_tablestore\_java\_sdk\_2.2.4.zip](https://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/31713/cn_zh/1463131862644/aliyun-ots-java-sdk-2.2.4.zip)

更新日志：

-   新增 condition update。
-   新增 filter。

## 版本号：2.1.2 { .section}

发布时间：2015-12-31

下载地址：[aliyun\_ots\_java\_sdk\_2.1.2.zip](https://ots-public-sdk.oss-cn-hangzhou.aliyuncs.com/aliyun-ots-java-sdk-2.1.2.zip)

更新日志：根据按量计费方式，重新调整了示例代码中的预留 CU 设置。

## 版本号 2.1.1 { .section}

发布时间：2015-12-30

下载地址：[aliyun-ots-java-sdk-2.1.1.zip](https://ots-public-sdk.oss-cn-hangzhou.aliyuncs.com/aliyun-ots-java-sdk-2.1.1.zip)

更新日志：由于 JodaTime 2.4 在 Java 8 下有序列化时间格式不正确的 bug，所以将 SDK 依赖的 JodaTime 版本从 2.4 提升到 2.9.1。

## 版本号 2.1.0 { .section}

发布时间：2015-11-12

下载地址：[aliyun-ots-java-sdk-2.1.0.zip](https://ots-public-sdk.oss-cn-hangzhou.aliyuncs.com/aliyun-ots-java-sdk-2.1.zip)

更新日志：

-   网络传输异步化及一系列性能调优：同等 CPU 使用情况下 QPS 提升数倍。
-   提供灵活易用的异步接口：支持传入 callback，同时返回 future。
-   解除与 OSS SDK 的绑定：新的 SDK 只包含表格存储 SDK 相关代码，目录结构有细微调整。
-   重试逻辑优化：优化默认的重试逻辑；支持 batch 操作中单行错误单独重试；提供更清晰的重试逻辑自定义方式。
-   日志优化：支持请求发送到接收的各个环节的日志记录；支持记录慢请求日志；通过 TraceId 打通 SDK 与后端服务的全链条日志。
-   提供支持批量数据导入的 OTSWriter 接口：旨在提供易用、高效的数据导入体验。
-   其他优化：丰富各种数据类型的工具函数；提供计算数据大小的接口等。

 **重要提示**：

2.1.0 与 2.0.4 版本有细微不兼容之处，如下所示，详情请参见[老版 SDK 迁移教程](https://bbs.aliyun.com/read/262897.html)。

-   替换新的 SDK 后需要更改少数几个类的 import 路径。因为有几个类的 package 有调整，比如 ClientConfiguration 的 package 由 com.aliyun.openservices 调整为 com.aliyun.openservices.ots。调整 package 的主要原因是表格存储的 SDK 已经与 OSS SDK 分离，之前公用的几个类放在 ots 的 package 下更为合理。
-   当您不再使用一个 OTSClient 实例时（比如程序结束前），需要主动调用 OTSClient 的 shutdown 方法，释放 OTSClient 对象占有的线程和连接资源。
-   ClientConfiguration 中部分配置项的名称有调整，比如在配置项名称中加入了时间单位。
-   新 SDK 的包依赖有变化，比如使用了 HttpAsyncClient 和 Jodatime 等，如果您在运行中有遇到问题，需要考虑是否引入了冲突的依赖。

## 版本号 2.0.4 { .section}

发布时间：2015-09-25

下载地址：[aliyun-ots-java-sdk-2.0.4.zip](https://ots-public-sdk.oss-cn-hangzhou.aliyuncs.com/aliyun-openservices-OTS-2.0.4.zip)

