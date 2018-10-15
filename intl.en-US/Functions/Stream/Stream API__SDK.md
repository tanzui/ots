# Stream API/SDK {#concept_60305_zh .concept}

## API { .section}

-   Enable and disable Stream

    You can specify whether Stream is enabled or disabled when creating a table. Also, you can use the `UpdateTable` operation to enable or disable Stream later. The `CreateTable` and `UpdateTable` operations now include a `StreamSpecification` parameter that allows you to set Stream parameters:

    -   enable\_stream: Whether to enable Stream.
    -   expiration\_time: Stream data expiration time. Expired modification log entries are deleted.
-   Read modification logs

    To read Stream data, follow these steps:

    1.  Call `ListStreams` to obtain the current table’s Stream information, such as Stream ID. For more information, see [ListStream](../../../../reseller.en-US/API Reference/Operations/ListStream.md#).
    2.  Call `DescribeStream` to obtain the current Stream’s data shard information, such as the shard list. Each shard log contains shard information such as the parent shard and shardID. For more information, see [DescribeStream](../../../../reseller.en-US/API Reference/Operations/DescribeStream.md#).
    3.  After obtaining StreamID and shardID, use `GetShardIterator` to obtain the current shard’s read iterator value. This value marks the starting position for reading the shard log. For more information, see [GetShardIterator](../../../../reseller.en-US/API Reference/Operations/GetShardIterator.md#).
    4.  Call `GetStreamRecord` to read the specific modification logs. Each call returns a new iterator for the next read to use. For more information, see [GetStreamRecord](../../../../reseller.en-US/API Reference/Operations/GetStreamRecord.md#).
    **Note:** 

    -   Operations made to the same primary key have to be sequenced. Stream makes sure that operations made to the same shard are sequenced. However, shards may be split or merged, so before you read the data of a shard, make sure that data of the shard’s parent shard and parent\_sibling has been read.
    -   When an empty `NextShardIterator` is returned, it indicates that incremental data in the current shard has been fully read. This situation occurs typically when the shard is inactive after a split or merge operation. When a shard has been fully read, you can call `DescribeStream` again to retrieve information about the new shard.

## SDK { .section}

Table Store Java SDK supports the Stream interface. For more information, see [Java SDK](../../../../reseller.en-US/SDK Reference/Java SDK/Preface.md#).

