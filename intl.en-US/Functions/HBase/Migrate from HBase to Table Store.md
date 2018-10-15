# Migrate from HBase to Table Store {#concept_50127_zh .concept}

The following information explains how to migrate HBase to Table Store.

## Dependencies {#section_alf_thn_cfb .section}

Table Store HBase Client v1.2.0 depends on HBase Client v1.2.0 and Table Store Java SDK v4.2.1. The configuration of `pom.xml` is as follows.

```language-xml
 <dependencies>
        <dependency>
            <groupId>com.aliyun.openservices</groupId>
            <artifactId>tablestore-hbase-client</artifactId>
            <version>1.2.0</version>
        </dependency>
    </dependencies>

```

If you want to use another HBase Client or Table Store Java SDK version, you must use the exclusion tag. In the following example, HBase Client v1.2.1 and Table Store Java SDK v4.2.0 are used.

```language-xml
   <dependencies>
        <dependency>
            <groupId>com.aliyun.openservices</groupId>
            <artifactId>tablestore-hbase-client</artifactId>
            <version>1.2.0</version>
            <exclusions>
                <exclusion>
                    <groupId>com.aliyun.openservices</groupId>
                    <artifactId>tablestore</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.apache.hbase</groupId>
                    <artifactId>hbase-client</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.apache.hbase</groupId>
            <artifactId>hbase-client</artifactId>
            <version>1.2.1</version>
        </dependency>
        <dependency>
            <groupId>com.aliyun.openservices</groupId>
            <artifactId>tablestore</artifactId>
            <classifier>jar-with-dependencies</classifier>
            <version>4.2.0</version>
        </dependency>
    </dependencies>

```

Table Store HBase Client v1.2.x is only compatible with HBase Client v1.2.x, because API changes exist in HBase Client v1.2.x and earlier.

If you want to use HBase Client version v1.1.x, use Table Store HBase Client version v1.1.x.

If you want to use HBase Client version v0.x.x, see [Migrate HBase of an earlier version](reseller.en-US/Functions/HBase/Migrate HBase of an earlier version.md#).

## Configure the file { .section}

To migrate data from HBase Client to Table Store HBase Client, modify the following two items in the configuration file.

-   HBase Connection type

    Set Connection to TableStoreConnection.

    ```language-java
        <property>
            <name>hbase.client.connection.impl</name>
            <value>com.alicloud.tablestore.hbase.TablestoreConnection</value>
        </property>
    
    ```

-   Configuration items of Table Store

    Table Store is a cloud service and provides strict permission management. Table Store offers strict permission management. To access Table Store, you must configure access information such as the AccessKey.

    -   You need to configure the following four items before accessing Table Store:

        ```language-xml
         <property>
        			<name>tablestore.client.endpoint</name>
        			<value></value>
        		</property>
        		<property>
        			<name>tablestore.client.instancename</name>
        			<value></value>
        		</property>
        		<property>
        			<name>tablestore.client.accesskeyid</name>
        			<value></value>
        		</property>
        		<property>
        			<name>tablestore.client.accesskeysecret</name>
        			<value></value>
        		</property>
        
        ```

    -   Optional items you can configure are as follows.

        ```language-xml
        		<property>
        			<name>hbase.client.tablestore.family</name>
        			<value>f1</value>
        		</property>
        		<property>
        			<name>hbase.client.tablestore.family.$tablename</name>
        			<value>f2</value>
        		</property>
        		<property>
        			<name>tablestore.client.max.connections</name>
        			<value>300</value>
        		</property>
        		<property>
        			<name>tablestore.client.socket.timeout</name>
        			<value>15000</value>
        		</property>
        		<property>
        			<name>tablestore.client.connection.timeout</name>
        			<value>15000</value>
        		</property>
        		<property>
        			<name>tablestore.client.operation.timeout</name>
        			<value>2147483647</value>
        		</property>
        		<property>
        			<name>tablestore.client.retries</name>
        			<value>3</value>
        		</property>
        
        ```

        -   hbase.client.tablestore.family and hbase.client.tablestore.family.$tablename

            -   Table Store only supports single ColumnFamilies. When you use HBase APIs, you must enter the content of the family.

                `hbase.client.tablestore.family` indicates global configuration, while `hbase.client.tablestore.family.$tablename` indicates configuration of a single table.

            -   Rule: For tables whose names are T, search for `hbase.client.tablestore.family.T` first. If the family does not exist, search for `hbase.client.tablestore.family`. If the family does not exist, use the default value f.
        -   tablestore.client.max.connections

            Maximum connections. The default value is 300.

        -   tablestore.client.socket.timeout

            Socket time-out time. The default value is 15 seconds.

        -   tablestore.client.connection.timeout

            Connection time-out time. The default value is 15 seconds.

        -   tablestore.client.operation.timeout

            API time-out time. The default value is Integer.MAX\_VALUE, indicating that the API never times out.

        -   tablestore.client.retries

            Number of retries when a request fails. The default value is 3.


