# Introduction {#concept_89885_zh .concept}

## Overview {#section_e5r_sug_mys .section}

The Timeline model is a data model designed for message data scenarios. The model supports some special requirements of message data scenarios, such as message order preservation, storage of large numbers of messages, and real-time synchronization. The model also supports the full-text search and bool query. The model is applicable to message scenarios such as instant messaging \(IM\) and Feed streams.

## Architecture {#section_587_1gj_t5j .section}

The Timeline model provides clear core modules in a **simple** design. You can easily use this model, and set the model according to your business. The architecture of the model includes the following components:

-   Store: a store of Timeline data. The store is similar to a table in a database.
-   Identifier: an identifier used to identify Timeline data.
-   Meta: the metadata used to describe Timeline data. The metadata is stored in a free-schema structure and can contain any column.
-   Queue: stores all messages in a Timeline.
-   SequenceId: the serial number of a message body in the Queue. The SequenceId values must be incremental and unique. The Timeline model generates SequenceId values by using an auto-increment column. You can also specify SequenceId values by manual.
-   Message: the message body in the Timeline. The message is stored in a free-schema structure and can contain any column.
-   Index: includes Meta Index and Message Index. You can customize indexes for any columns in Meta or Message to provide the bool query.

## Features {#section_fuq_dov_tsf .section}

The Timeline model supports the following features:

-   Manages Meta data and messages, including basic data operations such as create, read, update, and delete.
-   Supports the bool query and full-text search for Meta data and messages.
-   Generates SequenceId values in two ways: auto-increment column and manual setting.
-   Supports the Timeline Identifier that contains multiple columns.
-   Compatible with the Timeline 1. X model. The TimelineMessageForV1 example of the Timeline model can directly read messages from and write messages to the V1 version.

 **Timeline** 

``` {#codeblock_guk_aqn_jni}
<dependency>
    <groupId>com.aliyun.openservices.tablestore</groupId>
    <artifactId>Timeline</artifactId> 
    <version>2.0.0</version> 
</dependency>
```

 **Table Store Java SDK \(integrated with the Timeline model\)** 

``` {#codeblock_45f_b8g_06h}
<dependency>
  <groupId>com.aliyun.openservices</groupId> 
  <artifactId>tablestore</artifactId> 
  <version>4.12.1</version> 
</dependency>
```

