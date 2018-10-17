# ReservedThroughput {#reference229 .reference}

ReservedThroughput indicates the provisioned throughput capacity reserved for read/write value set for a table.

## Data structure {#section_g5f_stq_dfb .section}

```language-xml
message ReservedThroughput {
    required CapacityUnit capacity_unit = 1;
}

```

capacity\_unit:

-   Type: [CapacityUnit](reseller.en-US/API Reference/Data Types/CapacityUnit.md#)

-   The current reserved read/write throughput value.


## Related operations { .section}

[CreateTable](reseller.en-US/API Reference/Operations/CreateTable.md#)

[UpdateRow](reseller.en-US/API Reference/Operations/UpdateRow.md#)

 [DescribeTable](reseller.en-US/API Reference/Operations/DescribeTable.md#) 

