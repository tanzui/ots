# Installation {#concept_43007_zh .concept}

## Prerequisites {#section_ach_41d_2fb .section}

Applicable to JDK 6 and later.

## Installation methods { .section}

-   Install Java SDK by using Maven

    To use the Table Store Java SDK in Maven, you only need to add the corresponding dependency to the pom.xml file. Java SDK v4.3.1 is used as an example. Type the following content in the “dependency” section:

    ```language-xml
    <dependency>
        <groupId>com.aliyun.openservices</groupId>
        <artifactId>tablestore</artifactId>
        <version>4.3.1</version>
    </dependency>
    
    ```

-   Install Java SDK by importing the JAR package to Eclipse

    For Java SDK v4.2.1, the process is as follows:

    1.  Download the [Java SDK](https://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/43005/cn_zh/1484715297472/tablestore-4.2.1.zip).
    2.  Decompress the SDK.
    3.  Copy the `tablestore-<versionId>.jar` file in the decompressed folder and copy all the files in the lib folder to your project.
    4.  In Eclipse, right-click your project and choose **Properties** \> **Java Build Path** \> **Add JARs** from the context menu.
    5.  Select all JAR files that you have copied in Step 3.
    After completing the preceding steps, you can use the Table Store Java SDK in Eclipse.


## Sample programs { .section}

The Table Store Java SDK provides diverse sample programs for your reference or use. You can get the sample program as follows:

1.  Download and decompress the Table Store Java SDK.
2.  Find the sample programs in the examples folder.

