# Incremental data operations {#concept_52791_zh .concept}

Table Store provides the ListStream and DescribeStream operations for streams and GetShardIterator and GetStreamRecord operations for shards.

## ListStream {#section_p23_yj2_2fb .section}

ListStream is used to list all streams enabled for a table on the current instance.

**Example**

List information about all streams enabled for a table.

```language-java
private static void listStream(SyncClient client, String tableName) {
	ListStreamRequest listStreamRequest = new ListStreamRequest(tableName);
	ListStreamResponse result = client.listStream(listStreamRequest);
}

```

## DescribeStream { .section}

DescribeStream is used to query creationTime, expirationTime, status, shards, and the ID of the next start shard \(if shards are not returned\) of a stream.

**Example 1**

Obtain information about all shards of the current stream.

```language-java
	private static void describeStream(SyncClient client, String streamId) {
		DescribeStreamRequest desRequest = new DescribeStreamRequest(streamId);
		DescribeStreamResponse desStream = client.describeStream(desRequest);
	}

```

**Example 2**

Set InclusiveStartShardId and the maximum number of shards returned each time.

```language-java
	private static void describeStream(SyncClient client, String streamId) {
	DescribeStreamRequest dsRequest = new DescribeStreamRequest(streamId);
	dsRequest.setInclusiveStartShardId(startShardId);
	dsRequest.setShardLimit(10);
	DescribeStreamResponse dscStream = client.describeStream(dsRequest);
	}

```

## GetShardIterator { .section}

GetShardIterator is used to obtain the start iterator for reading a shard.

**Example**

Obtain the start iterator for reading a shard.

```language-java
	private static void getShardIterator(SyncClient client, String streamId, String shardId) {
	GetShardIteratorRequest getShardIteratorRequest = new GetShardIteratorRequest(streamId, shardId);
	GetShardIteratorResponse shardIterator = client.getShardIterator(getShardIteratorRequest);
	}

```

## GetStreamRecord { .section}

GetStreamRecord is used to obtain each update record of a shard.

**Example**

Obtain the first 100 update records of a shard.

```language-java
	private static void getShardIterator(SyncClient client, String shardIterator) {
		GetStreamRecordRequest streamRecordRequest = new GetStreamRecordRequest(shardIterator);
		streamRecordRequest.setLimit(100);
		GetStreamRecordResponse streamRecordResponse = client.getStreamRecord(streamRecordRequest);
		List<StreamRecord> records = streamRecordResponse.getRecords();
		for(int k=0;k<records.size();k++){
			System.out.println("record info:" +  records.get(k).toString());
		}
		System.out.println("next iterator:" + streamRecordResponse.getNextShardIterator());
	}

```

