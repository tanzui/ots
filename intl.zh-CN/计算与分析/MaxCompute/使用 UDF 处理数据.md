# 使用 UDF 处理数据 {#concept_59866_zh .concept}

如果您在表格存储里面的数据有着独特的结构，希望自定义开发逻辑来处理每一行数据，比如解析特定的 json 字符串，可以使用 UDF（User Defined Function，即用户自定义函数）来处理。

## 操作步骤 {#section_n55_1kj_dfb .section}

1.  参考[MaxCompute Studio](https://www.alibabacloud.com/help/doc-detail/50889.htm) 文档，在 IntelliJ 中安装 MaxCompute-Java/MaxCompute-Studio 插件。插件安装完毕，就可以直接开发。

    下图是一个简单的 UDF 定义，将两个字符串连接。MaxCompute 支持更复杂的 UDF，包括自定义窗口执行逻辑等，更多信息请参考[开发和调试 UDF](https://www.alibabacloud.com/help/doc-detail/50902.htm)。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20330/153803558912003_zh-CN.png)

2.  打包之后可以上传到 MaxCompute。

    选择 **File** \> **Project Structure** \> **Artifacts**，输入Name 和 Output directory 后，单击 **+** 选择输出模块。打包后通过 ODPS Project Explorer 来上传资源、创建函数，然后就可以在 SQL 中调用。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20330/153803558912004_zh-CN.png)

3.  运行 bin/odpscmd.bat。

    ```
    // 我们选出来1行数据，并将name/name传入UDF，返回两个string的累加
    select cloud_metric_extract_md5(name, name) as udf_test from test_table limit 1;
    
    ```

    返回结果如下：

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20330/153803558912005_zh-CN.png)


