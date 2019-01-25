# Billing items and pricing {#concept_w4k_2tj_bfb .concept}

## Billing items {#section_rtn_4yd_cfb .section}

Billing items of Table Store include Data storage, Reserved read/write throughput, Additional read/write throughput and Downstream Internet traffic.

-   Data Storage

    Data storage fees are based on the total volume of instance data. Fees are calculated on a per hour basis. Due to fluctuations in the total data volume utilized, Table Store collects the total data volume of all table partitions at regular intervals and calculates the average hourly total data volume. This average volume value is then multiplied by the unit price to account for the actual storage fee. For more information about the data storage, see [Data storage](intl.en-US/Pricing/Data storage.md#).

-   Reserved read/write throughput

    The reserved read/write throughput is an attribute of a table. Setting an accurate reserved read/write throughput for your data tables helps you manage the costs of resource usage.

    Capacity instances do not support reserved read/write throughput. Table Store charges for the total reserved read/write throughput of all tables in a high-performance instance. Fees are calculated per hour. Because the configured reserved read/write throughput may fluctuate, Table Store collects the reserved read/write throughput of the tables at regular intervals to calculate the hourly average throughput. The average value is then multiplied by the unit price to reflect the actual hourly fee.

-   Additional read/write throughput

    The additional read/write throughput is the portion of read/write throughput actually consumed that exceeds the reserved read/write throughput, and is measured per second.

    Table Store accumulates the additional read throughput and write throughput of all tables in an instance during every billing cycle. The actual consumed additional throughput is multiplied by the corresponding unit price to determine the actual charge amount.

-   Downstream Internet traffic

    Table Store charges fees when applications access Table Store and generate downstream Internet traffic. Typically, an application’s use of HTTP to access responses returned by Table Store is what generates the majority of downstream Internet traffic charges. Even if the operation fails, the error information returned by Table Store still produces downstream traffic, which is billable.

    Table Store does not charge for intranet downstream traffic, or Internet upstream traffic.

    **Note:** Access across different regions also counts as Internet access.


The descriptions of billing items and pricing of Table Store are shown as follows:

|Billing items|Billing standard|Billing method|
|:------------|:---------------|:-------------|
|Data Storage|Total fees of data per instance| Pay-As-You-Go

 |
|Reserved read/write throughput|Fees for reserved read/write throughput that you configure|Pay-As-You-Go|
|Additional read/write throughput|The additional read/write throughput is the portion of read/write throughput actually consumed that exceeds the reserved read/write throughput| Pay-As-You-Go

 |
|Internet downstream traffic|Fees for Internet outbound traffic|Pay-As-You-Go|

**Note:** 

-   For more information about the billing standard, see [Table Store pricing details](https://www.alibabacloud.com/product/table-store/pricing).
-   The unit price of these billing items may change from time to time. For more information, see [Table Store pricing details](https://www.alibabacloud.com/product/table-store/pricing).

## Billing description {#section_rhq_g12_cfb .section}

The form store provides a way to pay by volume. The descriptions of billing methods, expiration/overdue payment and renewal are as follows:

|Method|Description|Expiration/Overdue payment description|Renewal description|
|:-----|:----------|:-------------------------------------|:------------------|
|Pay-As-You-Go| -   Billing per hour.
-   The system generates a bill after a table is created.

 | -   Fees are calculated on an hourly basis. If funds are unavailable to rectify your calculated bill, you are notified through your preferred contact method \(SMS or email\). The severity of the effect to your account of overdue account payments increases as follows:
-   Within 15 days of your payment being overdue, and after a notification has been sent, your services remain unaffected.
-   If your payment has been overdue for more than 15 days and the account has not been rectified, Alibaba Cloud suspends your service and freezes your Table Store. Your data remains stored in the system, and remains billable. At the same time, you will be reminded by SMS and email.
-   If your payment has been overdue for more than 30 days and your account has not been rectified, Alibaba Cloud stops providing any additional services. Any data stored in your Table Store is deleted and unrecoverable. You are notified through your preferred contact method \(SMS or email\) one day before the data is deleted.

 |A Pay-As-You-Go instance is billed according to the actual usage time. Renewal is not required.|

