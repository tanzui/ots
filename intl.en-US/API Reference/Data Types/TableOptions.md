# TableOptions {#reference543 .reference}

TableOptions indicates the table parameters, including TimeToLive and MaxVersions.

## Data structure { .section}

```language-protobuf
message TableOptions {
    optional int32 time_to_live = 1; // It can be dynamically modified
    optional int32 max_versions = 2; // It can be dynamically modified
    optional int64 deviation_cell_version_in_sec = 5; // It can be dynamically modified
}

```

time\_to\_live:

-   Type: int32

-   The retention time of data stored in this table \(unit: second\).


max\_versions:

-   Type: int32

-   The maximum number of versions stored in this table.


deviation\_cell\_version\_in\_sec:

-   Type: int64

-   The maximum version deviation. This parameter is used to forbid writing data that deviates from the expected output. For example, if the value of deviation\_cell\_version\_in\_sec is 1000 and the current time stamp is 10000, the allowed time stamp range to be written is \[10000 – 1000, 10000 + 1000\].


