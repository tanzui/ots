# UpdateTable {#concept_dgm_5bh_1gb .concept}

You can call this operation to modify TableOptions and ReservedThroughput.

## Parameters {#section_dv5_2ch_1gb .section}

-   **TableOptions**

    TableOptions includes the TTL, MaxVersions, and MaxTimeDeviation parameters for a table.

    |Parameter|Definition|Description|
    |:--------|:---------|:----------|
    |TTL|Time To Live, the life span of data.|     -   Unit: seconds.
    -   If you do not want data to expire, set TTL to -1.
    -   Table Store checks whether data has expired based on the data timestamp, current time, and table TTL. Data is expired and deleted if `current time - data timestamp > table TTL`. For more information about timestamps, see [Preface](../../../../../intl.en-US/Data Models/Preface.md#).
    -   When you write data with TTL, make sure that you specify a valid timestamp. We recommend that you set MaxTimeDeviation to specify the timestamp.
 |
    |MaxTimeDeviation|The maximum time deviation between the timestamp of written data and current system time.|     -   The system generates a timestamp for new data by default. The system checks whether data is expired based on the timestamp and TTL. You can specify the timestamp of written data. When the deviation between the timestamp you have specified and current time is greater than the specified TTL, the written data expires immediately. To avoid this problem, you can set MaxTimeDeviation.
    -   Unit: seconds.
 |
    |MaxVersions|The maximum number of versions that each attribute column can contain.|If the number of versions of an attribute column is more than the value of MaxVersions, Table Store only keeps the latest version that you have specified for MaxVersions.|

-   ReservedThroughput

    The configuration of reserved read and write throughput of a table.

    -   The interval of changing the value of ReservedThroughput is one minute.
    -   Table Store calculates billing according to the reserved read and write throughput you have specified in the ReservedThroughput field.
    -   If ReservedThroughput is higher than 0, Table Store calculates billing based on the reserved throughput and duration. The actual read and write throughput more than the reserved quota follows the Pay-As-You-Go billing method. For more information, see [Billing items and pricing](../../../../../intl.en-US/Pricing/Billing items and pricing.md#) to avoid unexpected fees.
    -   The default value 0 indicates that the Pay-As-You-Go billing method is fully applicable to Table Store.
    -   You can set the reserved read and write throughput of capacity instances only to 0. These instances do not allow reserved throughput.

## Example {#section_b2s_44l_cz .section}

Update TTL and Max Versions for the specified table.

```
private static void updateTable(SyncClient client) {
    int timeToLive = -1;
    int maxVersions = 5; // Update Max Versions to 5.
    TableOptions tableOptions = new TableOptions(timeToLive, maxVersions);
    UpdateTableRequest request = new UpdateTableRequest(TABLE_NAME);
    request.setTableOptionsForUpdate(tableOptions);
    client.updateTable(request);
}
```

