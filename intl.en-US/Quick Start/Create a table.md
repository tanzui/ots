# Create a table {#concept_55212_zh .concept}

## Use the console to create a table {#section_a1q_knf_cfb .section}

1.  Log on to the [Table Store console](https://partners-intl.console.aliyun.com/#/ots).
2.  Locate and click your target instance, or click **Manage** in the Actions column to go to the Instance Details page.
3.  Click **Create Table**.

    **Note:** Each instance supports up to 64 tables.

4.  Enter the required information.
    -   Each table name in an instance must be unique.

    -   If the table is created in a high-performance instance, you must specify the reserved read throughput and write throughput. The reserved throughput can be set to 0 CU. Configuring the reserved read/write throughput does not affect the table’s read/write performance and service capability.

        **Note:** If the reserved throughput is not set to 0 CU, billing based on the reserved throughput begins immediately after the table is created.

    -   Up to four primary keys can be set. The configuration and order of the primary keys cannot be changed.
5.  Click **OK**.
6.  You are directed to the Instance Details page once a table is successfully created. If your created table is not immediately displayed in the table list, click **Refresh** until it is displayed.

## Use the SDK to create a table {#section_i5g_vpf_cfb .section}

See the [SDK reference](../../../../reseller.en-US/SDK Reference/SDK overview.md#).

