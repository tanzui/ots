# ListStream {#reference555 .reference}

ListStream obtains information about the stream that is enabled for all tables on the current instance.

## Request structure: { .section}

```language-pb
message ListStreamRequest {
	optional string table_name = 1;
}

```

table\_name:

-   Type: optional string

-   The name of the table for which the current stream is enabled.


## Response message structure: { .section}

```language-pb
message ListStreamResponse {
    repeated Stream streams = 1;
}

message Stream {
    required string stream_id = 1;
    required string table_name = 2;
    required int64 creation_time = 3;
}

```

stream\_id:

-   Type: required string

-   The ID of the current stream.


table\_name:

-   Type: required string

-   The name of the table for which the current stream is enabled.


creation\_time:

-   Type: required int64

-   The time when the current stream is enabled.


