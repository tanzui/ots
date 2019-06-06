# ComputeSplitPointsBySize {#reference2074 .reference}

Logically splits data in a table into several shards whose sizes are close to the specified size, and returns the split points between the shards and prompt about machines where the shards are located. This API is generally used for execution plans like concurrency of plans on a computing engine.

## Request structure {#section_8pu_eqe_ogg .section}

``` {#codeblock_wih_s0k_sj3}
message ComputeSplitPointsBySizeRequest {
    required string table_name = 1;
    required int64 split_size = 2; // in 100MB
}
			
```

table\_name:

-   Type: string
-   Required parameter: Yes
-   The name of the table holding the data to be split.

split\_size:

-   Type: int64
-   Required parameter: Yes
-   The approximate size of each shard, in the unit of 100 MB.

## Response message structure {#section_is6_9gh_gis .section}

``` {#codeblock_f3r_znm_uix}
message ComputeSplitPointsBySizeResponse {
    required ConsumedCapacity consumed = 1;
    repeated PrimaryKeySchema schema = 2;

    /**
     * Split points between splits in an increasing order
     *
     * A split is a consecutive range of primary keys,
     * whose data size is about split_size specified in the request.
     * The size could be hard to be precise.
     *
     * A split point is an array of primary-key column w.r.t. table schema,
     * which is never longer than that of table schema.
     * Tailing -inf will be omitted to reduce transmission payloads.
     */
    repeated bytes split_points = 3;

    /**
     * Locations where splits lies in.
     *
     * By the managed nature of TableStore, these locations are no more than hints.
     * If a location is not suitable to be seen, an empty string will be placed.
     */
     message SplitLocation {
         required string location = 1;
         required sint64 repeat = 2;
     }
     repeated SplitLocation locations = 4;
}
			
```

consumed:

-   Type: ConsumedCapacity
-   The service capacity units consumed by this request.

schema:

-   Type: PrimaryKeySchema
-   The table’s schema, same as the schema given at the time of table creation.

split\_points:

-   Type: repeated bytes
-   The split points between shards. The split points must increase monotonically between the shards. Each split point is a [Plainbuffer-encoded](intl.en-US/API Reference/Data Types/PlainBuffer.md#) line and contains only primary keys. The last -inf of each split point is not transmitted for a smaller volume of data transmitted.

locations:

-   Type: repeated SplitLocation
-   The prompt about the machines where the split points are located. It can be null.

For example, a table contains three columns of primary keys, where the first column is of string type. After this API is called, five shards are obtained, which are:`(-inf,-inf,-inf)` to `("a",-inf,-inf)`， `("a",-inf,-inf)` to `("b",-inf,-inf)`， `("b",-inf,-inf)` to `("c",-inf,-inf)`， `("c",-inf,-inf)` to `("d",-inf,-inf)` and `("d",-inf,-inf)` to `(+inf,+inf,+inf)`. The first three shards are located in “machine-A”, while the last two in “machine-B”. In this case, split\_points is \(example\) `[("a"),("b"),("c"),("d")]`, while locations is \(example\) `"machine-A"*3, "machine-B"*2`.

## Capacity unit consumption {#section_4tw_ake_72l .section}

The number of consumed read service capacity units is the same as that of the shards. No write service capacity unit is consumed.

