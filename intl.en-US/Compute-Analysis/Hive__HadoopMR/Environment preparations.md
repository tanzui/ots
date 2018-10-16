# Environment preparations {#concept_45022_zh .concept}

## Access Table Store tables with Hive and HadoopMR {#section_xf3_2sn_dfb .section}

## Install JDK-7+ { .section}

1.  Download and install the relevant installation package of JDK-7+.
    -   Linux/MacOS: Use the package installation manager.

    -   Windows: [Click to download](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

2.  Check the installation status as follows.

    ```
    	$ java -version
    	java version "1.8.0_77"
    	Java(TM) SE Runtime Environment (build 1.8.0_77-b03)
    	Java HotSpot(TM) 64-Bit Server VM (build 25.77-b03, mixed mode)
    
    ```


## Install Hadoop { .section}

1.  Download [Hadoop](http://mirrors.cnnic.cn/apache/hadoop/common/) \(version 2.6.0 or later\).
2.  Unpack the installation package and install Hadoop for your cluster.
3.  Run Hadoop as follows.

    ```
    $ bin/start-all.sh
    # Check the hadoop service
    $ jps
    24017 NameNode
    24835 Jps
    24131 DataNode
    24438 ResourceManager
    5114 HMaster
    24287 SecondaryNameNode
    24527 NodeManager
    
    ```

4.  Add the path of Hadoop to `/etc/profile` and run `source /etc/profile` to make the configuration take effect.

    ```
    export HADOOP_HOME=/data/hadoop/hadoop-2.6.0
    export PATH=$PATH:$HADOOP_HOME/bin
    
    ```


## Install Hive { .section}

1.  Download [Hive](https://hive.apache.org/downloads.html), specifically the bin.tar.gz type.
2.  Unpack the installation package as follows.

    ```
    $ mkdir /home/admin/hive-2.1.0
    $ tar -zxvf apache-hive-2.1.0-bin.tar.gz -C /home/admin/
    $ mv /home/admin/apache-hive-2.1.0-bin /home/admin/hive-2.1.0/
    
    ```

3.  Initialize the schema as follows.

    ```
    	# Enter the specified directory
    	$ cd /home/admin/hive-2.1.0/
    
    	# Initialization, Derby can be replaced directly with mysql if it is MySQL
    	# If an error occurs, you can delete it by running rm -rf metastore_db/ and execute again.
    	$ ./bin/schematool -initSchema -dbType derby
    
    ```

4.  Run Hive as follows.

    ```
    $ ./bin/hive
    # check hive
    hive> show databases;
    OK
    default
    Time taken: 0.207 seconds, Fetched: 1 row(s)
    
    ```


## Download Java SDK for Table Store { .section}

1.  Download the [Java SDK](http://repo.maven.apache.org/maven2/com/aliyun/openservices/tablestore/4.3.1/tablestore-4.3.1-jar-with-dependencies.jar) dependency package \(version 4.1.0 or later\).

    **Note:** The SDK dependency package is updated with [Java SDK](../../../../reseller.en-US/SDK Reference/Download SDK/Java SDK.md#). Download the dependency package according to the latest Java SDK.

2.  Copy the SDK to the Hive directory as follows.

    ```
    $ mv tablestore-4.1.0-jar-with-dependencies.jar /home/admin/hive-2.1.0/
    
    ```


## Download EMR dependency package { .section}

1.  Download the [Alibaba Cloud EMR dependency package.](http://repo.maven.apache.org/maven2/com/aliyun/emr/emr-tablestore/1.4.2/emr-tablestore-1.4.2.jar)

    **Note:** For more information on EMR, click [here](https://github.com/aliyun/aliyun-emapreduce-sdk).

2.  Rename the emr-sdk\_2.10-1.3.0-20161025.065936-1.jar file.

    ```
    mv emr-sdk_2.10-1.3.0-20161025.065936-1.jar /home/admin/hive-2.1.0/emr-sdk_2.10-1.3.0-SNAPSHOT.jar
    ```


