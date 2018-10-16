# Environment preparations {#concept_45017_zh .concept}

## Access Table Store tables with Spark or Spark SQL {#section_td3_dg4_dfb .section}

You can use Spark and Spark SQL to access data in Table Store directly by using the dependency package released by Table Store and E-MapReduce.

## Install Spark/Spark SQL { .section}

1.  Download the [Spark installation package](http://spark.apache.org/downloads.html) that complies with the following requirements:
    -   Release version: 1.6.2
    -   Package type: Pre-built for Hadoop 2.6
    -   Download type: Direct Download
2.  Unpack the installation package as follows.

    ```
    $ cd /home/admin/spark-1.6.2
    $ tar -zxvf spark-1.6.2-bin-hadoop2.6.tgz
    
    ```


## Install JDK-7+ { .section}

1.  Download and install the installation package of JDK-7+.
    -   Linux/MacOS: Use the package installation manager.

    -   Windows: [Click to download](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

2.  Check the installation status as follows.

    ```
    $ java -version
    java version "1.8.0_77"
    Java(TM) SE Runtime Environment (build 1.8.0_77-b03)
    Java HotSpot(TM) 64-Bit Server VM (build 25.77-b03, mixed mode)
    
    ```


## Download Java SDK for Table Store { .section}

1.  Download the [Java SDK dependency package](http://repo.maven.apache.org/maven2/com/aliyun/openservices/tablestore/4.3.1/tablestore-4.3.1-jar-with-dependencies.jar) \(version 4.1.0. or higher\).

    **Note:** The SDK dependency package is updated with [Java SDK](../../../../reseller.en-US/SDK Reference/Download SDK/Java SDK.md#). Download the dependency package according to the latest Java SDK.

2.  Copy the SDK to the Spark directory as follows.

    ```
    $ mv tablestore-4.1.0-jar-with-dependencies.jar /home/admin/spark-1.6.2/
    
    ```


## Download EMR dependency package { .section}

-   Download the [Alibaba Cloud EMR dependency package](http://repo.maven.apache.org/maven2/com/aliyun/emr/emr-tablestore/1.4.2/emr-tablestore-1.4.2.jar).

    **Note:** For more information on EMR, click [here](https://github.com/aliyun/aliyun-emapreduce-sdk).

-   Rename the emr-sdk\_2.10-1.3.0-20161025.065936-1.jar file.

    ```
    mv emr-sdk_2.10-1.3.0-20161025.065936-1.jar /home/admin/spark-1.6.2/emr-sdk_2.10-1.3.0-SNAPSHOT.jar
    ```


## Run Spark SQL { .section}

```
$ cd /home/admin/spark-1.6.2/
$ bin/spark-sql --master local --jars tablestore-4.3.1-jar-with-dependencies.jar,emr-tablestore-1.4.2.jar

```

