# Timeline {#concept_89885_zh .concept}

Timeline 模型是针对消息数据场景所设计的数据模型，它能满足消息数据场景对消息保序、海量消息存储、实时同步的特殊需求，同时支持全文检索与多维度组合查询。可以同时应用在IM、Feed流等消息场景的实现上。

## 模型结构 {#section_587_1gj_t5j .section}

Timeline模型以**简单**为设计目标，核心模块构成比较清晰明了。模型尽量提升使用的自由度，让您能够根据自身场景需求选择更为合适的实现。模型的架构主要包括：

-   Store：Timeline存储库，类似数据库的表的概念。
-   Identifier：用于区分Timeline的唯一标识。
-   Meta：用于描述Timeline的元数据，元数据描述采用free-schema结构，可自由包含任意列。
-   Queue：一个Timeline内所有Message存储在Queue内。
-   SequenceId：Queue中消息体的序列号，需保证递增、唯一，模型支持自增列、自定义两种实现模式。
-   Message：Timeline内传递的消息体，是一个free-schema的结构，可自由包含任意列。
-   Index：包含Meta Index和Message Index，可对Meta或Message内的任意列自定义索引，提供灵活的多条件组合查询和搜索。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20277/156144337547301_zh-CN.png)

## 功能介绍 {#section_fuq_dov_tsf .section}

Timeline模型支持以下功能：

-   支持Meta、消息的基本管理（数据的CRUD）。
-   支持Meta、消息的多维组合查询、全文检索。
-   支持SequenceId的两种设置：自增列、手动设置。
-   支持多列的Timeline Identifier。
-   兼容Timeline 1.X模型，提供的TimelineMessageForV1样例可直接读、写V1版本消息。

 **Timeline** 

``` {#codeblock_guk_aqn_jni}
<dependency>
    <groupId>com.aliyun.openservices.tablestore</groupId>
    <artifactId>Timeline</artifactId>
    <version>2.0.0</version>
</dependency>
```

 **TableStore Java SDK\(模型已合入SDK\)** 

``` {#codeblock_45f_b8g_06h}
<dependency>
  <groupId>com.aliyun.openservices</groupId>
  <artifactId>tablestore</artifactId>
  <version>4.12.1</version>
</dependency>
```

