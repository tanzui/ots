# Stream API/SDK {#concept_60305_zh .concept}

## API { .section}

-   打开/关闭 Steam

    用户可以在创建表的时候设置 Stream 是否开启，也可以通过 `UpdateTable` 来开启或者关闭 Stream。`CreateTable` 和 `UpdateTable` 中新增了 `StreamSpecification` 参数，表示 Stream 的相关参数：

    -   enable\_stream：Stream 是否打开。
    -   expiration\_time：Stream 数据的过期时间，较早的修改记录将会被删除。
-   读取修改记录

    读取 Stream 数据的流程如下：

    1.  调用 `ListStreams` 获取当前表的 Stream 信息，例如 StreamID。详细信息请参见[ListStream](../../../../intl.zh-CN/.md#)。
    2.  调用 `DescribeStream` 获取当前 Stream 的数据分片信息，例如 shard 的列表，每个 shard 记录又包含父 shard 信息、shardID 信息等。详细信息请参见[DescribeStream](../../../../intl.zh-CN/.md#)。
    3.  获取 StreamID 和 shardID 后，通过 `GetShardIterator` 获取当前 shard 的读 iterator 值，这个值标记着读取该 shard 记录的起始位置。详细信息请参见[GetShardIterator](../../../../intl.zh-CN/.md#)。
    4.  调用 `GetStreamRecord` 来读取具体的修改记录，每次调用会返回新的 iterator，用于下次读取。详细信息请参见[GetStreamRecord](../../../../intl.zh-CN/.md#)。
    **说明：** 

    -   同一个主键下的操作必须有序。在同一个 shard 下，Stream 做了这个保证。但是 shard 会出现分裂、合并等操作，所以您在读取某个 shard 的数据时，需要确保他的父 shard 以及 parent\_sibling 的数据已经被读取。
    -   当读到空的 NextShardIterator 的时候，说明当前 shard 的增量数据已经全部读完，通常情况是该 shard 已经进入 inactive 的状态（发生分裂或者合并）。当一个 shard 已经被全部读完以后，您可以重新调用 `DescribeStream` 获取新的 shard 信息。

## SDK { .section}

为了方便您使用 Stream API，Table Store 的 Java SDK 已经支持 Stream 接口。详细信息请参见[Java SDK](../../../../intl.zh-CN/.md#)。

