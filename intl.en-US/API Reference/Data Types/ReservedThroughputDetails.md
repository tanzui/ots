# ReservedThroughputDetails {#reference614 .reference}

ReservedThroughputDetails indicates the provisioned throughput capacity reserved for read/write information for a table.

## Data structure {#section_pg1_f5q_dfb .section}

```language-xml
message ReservedThroughputDetails {
    required CapacityUnit capacity_unit = 1;
    required int64 last_increase_time = 2;
    optional int64 last_decrease_time = 3;
    required int32 number_of_decreases_today = 4;
}

```

capacity\_unit:

-   Type: [CapacityUnit](reseller.en-US/API Reference/Data Types/CapacityUnit.md#) 

-   The provisioned throughput capacity reserved for read/write value for the specified table.


last\_increase\_time:

-   Type: int64

-   The last time the provisioned throughput capacity reserved for read/write settings were raised for this table, expressed in UTC time at second level.


last\_decrease\_time:

-   Type: int64

-   The last time the provisioned throughput capacity reserved for read/write settings were lowered for this table, expressed in UTC time at second level.


number\_of\_decreases\_today:

-   Type: int32

-   The number of times the table’s provisioned throughput capacity reserved for read/write settings were lowered on the current calendar day.


## Related operations { .section}

[UpdateTable](reseller.en-US/API Reference/Operations/UpdateTable.md#) 

[DescribeTable](reseller.en-US/API Reference/Operations/UpdateTable.md#)

