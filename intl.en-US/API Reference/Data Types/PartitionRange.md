# PartitionRange {#reference348 .reference}

PartitionRange specifies the shard range.

```language-protobuf
message PartitionRange {
    required bytes begin = 1; // encoded as SQLVariant
    required bytes end = 2; // encoded as SQLVariant
}

```

begin:

-   Type: Bytes

-   The starting key of the shard. The shard is a left-closed right-open interval, which includes the starting key. It is in Plainbuffer format. For more information about encoding, see [Plainbuffer encoding](reseller.en-US/API Reference/Data Types/PlainBuffer.md#).


end:

-   Type: Bytes

-   The ending key of the shard. The shard is a left-closed right-open interval, which does not include the ending key. It is in Plainbuffer format. For more information about encoding, see [Plainbuffer encoding](reseller.en-US/API Reference/Data Types/PlainBuffer.md#).


