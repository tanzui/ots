# UpdateTable {#reference1270 .reference}

UpdateTable updates the reserved read or write throughput settings of the specified table. New settings take effect within one minute of a successful update.

## Request structure: { .section}

```language-pb
message UpdateTableRequest {
    required string table_name = 1;
    optional ReservedThroughput reserved_throughput = 2;
    optional TableOptions table_options = 3;
    optional StreamSpecification stream_spec = 4;
}

```

table\_name:

-   Type: String

-   Required parameter: Yes

-   The name of the table for which the reserved read/write throughput settings are to be modified.


reserved\_throughput:

-   Type: [ReservedThroughput](reseller.en-US/API Reference/Data Types/ReservedThroughput.md#) 

-   Required parameter: Yes

-   The reserved read/write throughput settings to be modified for the table. These settings take effect within one minute.

-   You may modify the table’s reserved read/write throughput settings as required.

-   In capacity\_unit, read and write must at least have a non-null value. Otherwise, the request fails and an error is returned.


table\_options:

-   Type: [TableOptions](reseller.en-US/API Reference/Data Types/TableOptions.md#) 

-   Required parameter: Yes

-   Sets TimeToLive and MaxVersions.


StreamSpecification

-   Type: [StreamSpecification](reseller.en-US/API Reference/Data Types/StreamSpecification.md#) 

-   Required parameter: No

-   Specifies whether to enable Stream-related attributes.


## Response message structure: { .section}

```language-pb
message UpdateTableResponse {
    required ReservedThroughputDetails reserved_throughput_details = 1;
    required TableOptions table_options = 2;
}

```

capacity\_unit\_details:

-   Type: [ReservedThroughputDetails](reseller.en-US/API Reference/Data Types/ReservedThroughputDetails.md#) 

-   After the update, in addition to containing the current reserved read/write throughput settings, the table’s reserved read/write throughput settings information also contains the time these settings were last updated and the number of times they have been lowered for the current date.


Note:

-   The minimum time interval between adjustments to a table’s reserved read/write throughput is two minutes. If the current UpdateTable operation is requested within two minutes of the previous operation, it is rejected.

-   A table’s reserved read/write throughput can be raised or lowered an unlimited number of times within a single day \(from 00:00:00 to 00:00:00 the next day in UTC time\).


table\_options:

-   Type: [TableOptions](reseller.en-US/API Reference/Data Types/TableOptions.md#) 

-   The latest value of the table\_options parameter after the modification.


