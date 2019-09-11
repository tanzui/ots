# Read and write data in the console {#concept_1180799 .task}

After a data table is created, you can read and write data in the console.

## Write data {#section_tcs_o80_zhe .section}

The procedure is as follows:

1.  Log on to the Table Store console. .
2.  Find the data table to which you want to write data, and click the table name. In the left-side navigation pane of the displayed Table Data page, click **Data Editor**.
3.  Click **Insert**.
4.  In the Insert dialog box, enter **Primary Key Value** and click **Add Column** to enter **Name**, **Value**, and **Version**. By default, **Version** is the current system time.
5.  Click **Insert**. The written data row is displayed on the Data Editor page.

    **Note:** The maximum data rows that can be displayed in the console is 50. This limit does not apply to SDKs.


## Delete data {#section_kbx_ii5_bte .section}

To delete data of a table, you can select the data rows to be deleted on the Data Editor page and click **Delete** in the upper-right corner of the page.

## Update data {#section_y5k_nm3_wbq .section}

You can update the attribute columns of a data row in the console. The procedure is as follows:

1.  On the Data Editor page, find and select the data row to be updated, and then click **Update**.
2.  In the Update dialog box, click **Add Column** to add a column to this row, or update an existing attribute column. To update an existing attribute column, find the target attribute column and select **Update Type**:

    |Update type|Description|Operation|
    |-----------|-----------|---------|
    |PUT|Updates the name, type, value, and version of the attribute column.|Set the update type to **PUT** and enter the new values.|
    |Delete|Deletes data of a specified version of the attribute column.|Set the update type to **DELETE** and select a version number to be deleted in the **Version** column.|
    |Delete ALL|Deletes data of all versions of the attribute column.|Set the update type to **ALL**.|

3.  Click **OK**.

## Read data {#section_e3e_5c4_g3w .section}

In the Table Store console, you can query data in a single row \(GetRow\) or query data within a specified range \(RangeQuery\). The procedure is as follows:

 **GetRow** 

1.  Find the data table to read data and click the table name. In the left-side navigation pane of the displayed Table Data page, click **Search**. The **Search** dialog box is displayed.
2.  Select the query range.

    In the Search dialog box, select **GetRow** from the **Mode** drop-down list.

3.  Select the attribute columns to be returned.

    To display all attribute columns, select **Display All**. To display the specified attribute column, clear **Display All**, enter the specified attribute column, and click **Add**.

    **Note:** The maximum attribute columns that can be specified in the console is 20. This limit does not apply to SDKs.

4.  Select the version to be returned.

    Enter **Max Versions** or select **Custom Versions** to specify the version range of the returned data.

    **Note:** The maximum number of versions to read is 10. This limit does not apply to SDKs.

5.  Specify the primary key column.

    Enter the complete **Primary Key Value** of the row to read. The integrity and accuracy of the primary key value affect the query results.

6.  Click **GetRow**. The data is displayed on the Data Editor page.

 **RangeQuery** 

1.  Find the table to read data and then click **Search** on the Data Editor page. The **Search** dialog box is displayed.
2.  Select the query range.

    In the Search dialog box, select **Range Search** from the **Mode** drop-down list.

3.  Select the attribute columns to be returned.

    To display all attribute columns, select **Display All**. To display the specified attribute column, clear **Display All**, enter the specified attribute column, and click **Add**. You can also select the number of **Items Per Page**.

    **Note:** The maximum attribute columns that can be specified in the console is 20. This limit does not apply to SDKs.

4.  Select the version to be returned.

    Enter **Max Versions** or select **Custom Versions** to specify the version range of the returned data.

    **Note:** The maximum number of versions to read is 10. This limit does not apply to SDKs.

5.  Set the primary key range for query. You can specify **Min Value** and **Max Value**, or enter a range in the **Custom** field.

    **Note:** 

    -   The first primary key value takes priority when the range query mode is used. When the minimum and maximum value of the first primary key are the same, the system will use the second primary key to perform the query. The query rules for the subsequent primary keys are the same as those for the first two primary keys.
    -   The range of Custom Version is left-open and right-closed.
6.  Select **Forward Search** or **Backward Search** as needed. The query result is displayed on the Data Editor page.

