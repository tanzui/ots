# ConsumedCapacity {#reference186 .reference}

ConsumedCapacity indicates capacity units consumed by an operation.

## Data structure {#section_sxm_cqq_dfb .section}

```language-xml
message ConsumedCapacity {
    required CapacityUnit capacity_unit = 1;
}

```

capacity\_unit:

-   Type: [CapacityUnit](reseller.en-US/API Reference/Data Types/CapacityUnit.md#) 

-   The value of capacity units consumed by this operation.


