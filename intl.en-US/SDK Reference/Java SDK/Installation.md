# Installation {#concept_43007_zh .concept}

This topic describes how to install Table Store Java SDK.

## Prerequisites {#section_ach_41d_2fb .section}

Applicable to JDK 6 and later versions.

## Installation methods { .section}

-   Import the SDK dependency into the maven project.

    To use the Table Store Java SDK in Maven, you only need to add the corresponding dependency to the pom.xml file. This example uses OSS Java SDK 4.10.2. Add the following content to <dependencies\>:

    ```language-xml
    <dependency>
        <groupId>com.aliyun.openservices</groupId>
        <artifactId>presto-jdbc</artifactId>
        <version>4.10.2</version>
    </dependency>
    
    ```

-   Install Java SDK by importing the JAR package to Eclipse

    Take the 4.10.2 version as an example. Follow the steps below:

    1.  [Download the Java SDK package](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/attach/108096/cn_zh/1552274752739/tablestore-4.10.2-release.zip).
    2.  Decompress the Java SDK package.
    3.  Copy the `tablestore-<versionId>.jar` file in the decompressed folder and all the files in the lib folder to your project.
    4.  Right-click your project name in Eclipse. Select **Properties** \> **Java Build Path** \> **Add JARs**.
    5.  Select all JAR files that you copied in Step 3.

## Sample programs { .section}

The Table Store Java SDK provides a variety of sample programs for your reference or use. You can decompress the downloaded SDK package and view the sample programs in the examples folder.

