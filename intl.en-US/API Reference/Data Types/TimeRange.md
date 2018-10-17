# TimeRange {#reference456 .reference}

TimeRange specifies the time stamp range or time stamp value during data query.

## Data structure { .section}

```language-protobuf
message TimeRange {
    optional int64 start_time = 1;
    optional int64 end_time = 2;
    optional int64 specific_time = 3;
}

```

start\_time:

-   Type: int64

-   The starting time stamp \(unit: millisecond\). The minimum and maximum values of the time stamp are 0 and INT64. MAX, respectively.


end\_time:

-   Type: int64

-   The ending time stamp \(unit: millisecond\). The minimum and maximum values of the time stamp are 0 and INT64. MAX, respectively.


specific\_time:

-   Type: int64

-   The specified time stamp. You can set either specific\_time, or \[start\_time, end\_time\). The unit for the time stamp must be millisecond. The minimum and maximum values of the time stamp are 0 and INT64. MAX, respectively.


