# Process data by using UDF {#concept_59866_zh .concept}

If your data stored in Table Store is uniquely structured and you want to define development logic to process each line of data \(for example, parsing a specific JSON string\), you can use User Defined Function \(UDF\).

## Procedure {#section_n55_1kj_dfb .section}

1.  Follow the [MaxCompute Studio](https://partners-intl.aliyun.com/help/product/50889.htm) to install MaxCompute-Java/MaxCompute-Studio plug-in in IntelliJ. Development may be started when the plug-in is installed.

    The following figure shows a simple UDF definition, which connects two strings. MaxCompute supports more complex UDF, for example, user-defined window execution logic. For more information, see [Develop and debug UDF](https://partners-intl.aliyun.com/help/product/50902.htm).

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20330/153968024312003_en-US.png)

2.  Upload the resource to MaxCompute after packaging.

    Select **File** \> **Project Structure** \> **Artifacts**. Enter the Name and the Output directory, then click **+** to select the output module. After packaging, upload the resource and create a function using ODPS Project Explorer, and then you can call it in SQL.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20330/153968024312004_en-US.png)

3.  Run bin/odpscmd.bat.

    ```
    // Select a line of data, and pass name/name into the UDF. A connection of the two strings is returned.
    select cloud_metric_extract_md5(name, name) as udf_test from test_table limit 1;
    
    ```

    The returned result is displayed as follows.

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20330/153968024312005_en-US.png)


