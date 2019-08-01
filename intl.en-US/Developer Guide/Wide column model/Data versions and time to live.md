# Data versions and time to live {#concept_z5t_jmj_bfb .concept}

## Version numbers {#section_mxc_2fg_4gb .section}

Each value of an attribute corresponds to a different version. The value of the version is the version number \(timestamp\). The version number is used to determine the [Time to live \(TTL\)](intl.en-US/Developer Guide/Wide column model/Data versions and time to live.md#section_cv2_kyf_cfb).

When writing data, you are allowed to specify the version number of an attribute. If you do not specify a version number, the time from Jan 1, 1970, 00:00:00 UTC to the present time will be converted to milliseconds and used as the version number of the attribute. Version numbers are measured in milliseconds. When performing a comparison between TTL properties or Max Version Offset properties, you are required to divide version numbers by 1000 to convert the unit to seconds. The version number is used in the following scenarios:

-   Time to live \(TTL\)

    The version number can be used to determine the lifecycle of a table. Assume that a version number of an attribute is 1468944000000, which is calculated based on the time of July 20, 2016, 00:00:00 UTC. When you set the TTL as 86400 \(one day\), the data of that version expires on July 21, 2016, 00:00:00 UTC. Then, the data is automatically deleted.

    When the version number of the data is determined by Table Store, the written data will be automatically cleansed after the specified TTL.

-   Read the version number of each row's data

    When Table Store reads a row of data, you can specify the maximum number of versions or the range of version numbers of each attribute, which are allowed to be read.


## Max Versions {#section_qsz_fyf_cfb .section}

When writing data, you can specify the version number of an attribute. The Max Versions property is used to determine how many versions of data of an attribute in a table can be retained. When the number of versions of an attribute exceeds the value of the Max Versions property, the data of the earliest version will be deleted asynchronously.

After creating a table, you are allowed to use the UpdateTable function to dynamically update the Max Versions property of the table.

**Note:** 

-   Data whose version exceeds the specified value of Max Versions is considered invalid. The data is neither visible to you nor being read, even if the data is not actually deleted.
-   Assume that you have decreased the value of Max Versions. When the number of versions exceeds the newly specified value of Max Versions, the earliest version will be deleted asynchronously.
-   Assume that you have increased the number of Max Versions. When the previous data whose version exceeds the previous value of Max Versions and has not been deleted, the data will be read.

## Max Version Offset {#section_pnh_3yf_cfb .section}

The Max Version Offset property is used to determine the maximum allowed offset between the specified version number and the current system time. The property is measured in seconds. When the offset between the timestamp you have specified and the present time is greater than the specified TTL of a table, the written data expires immediately. You can set the Max Version Offset to prevent this situation.

To ensure that data is written successfully, Table Store will check the version number of an attribute when processing write requests. The range of valid version numbers of an attribute is: `[The time when you write data - Max Version Offset, The time when you write data + Max Version Offset)`. The version number of an attribute is measured in milliseconds. After the version number is divided by 1000, the result that is measured in seconds must fall within this range. When a version number does not fall within the range, this write request fails.

Assume that the Max Version Offset property of a table is 86400 \(one day\). On July 21, 2016, 00:00:00 UTC, you are only allowed to write data whose version number is greater than 1468944000000, which is the result converted from July 20, 2016, 00:00:00 UTC, and less than 1469116800000, which is the result converted from July 22, 2016, 00:00:00 UTC. When the version number of an attribute in a row is 1468943999000, which is the result converted from July 19, 2016, 23:59:59 UTC, then the write request for the row fails.

## Time to live {#section_cv2_kyf_cfb .section}

Time to live \(TTL\) is a property of a table. TTL is used to determine the lifecycle of the data. It is measured in seconds. To reduce storage costs, Table Store removes data that exceeds the specified TTL in the background to decrease your storage space.

Assume that the specified TTL of a table is 86400 \(one day\). On July 21, 2016, 00:00:00 UTC, attributes whose version numbers are less than 1468944000000 expire, which is the result converted from July 20, 2016, 00:00:00 UTC. Table Store will automatically remove the data of these attributes.

**Note:** 

-   Data that exceeds the specified TTL is invalid data. The data is neither visible to you nor being read, even if the data is not actually deleted.
-   Assume that you decrease the TTL value. Some pieces of data will expire due to the decreased TTL value. The expired data is removed asynchronously.
-   Assume that you increase the TTL value. If data that exceeds the previous TTL has not been removed, the data will be read again.

