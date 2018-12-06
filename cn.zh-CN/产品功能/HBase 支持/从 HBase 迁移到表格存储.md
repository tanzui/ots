# 从 HBase 迁移到表格存储 {#concept_50127_zh .concept}

Table Store HBase Client 是基于 HBase Client 的封装，使用方法和 HBase Client 基本一致，但是也有一些事项需要注意。

## 依赖 {#section_alf_thn_cfb .section}

Table Store HBase Client 1.2.0 版本中依赖了 HBase Client 1.2.0 版本和 Table Store JAVA SDK 4.2.1 版本。pom.xml 配置如下：

```language-xml
 <dependencies>
        <dependency>
            <groupId>com.aliyun.openservices</groupId>
            <artifactId>tablestore-hbase-client</artifactId>
            <version>1.2.0</version>
        </dependency>
    </dependencies>

```

如果需要使用其他版本的 HBase Client 或 Table Store JAVA SDK，可以使用 exclusion 标签。下面示例中使用 HBase Client 1.2.1 版本和 Table Store JAVA SDK 4.2.0 版本。

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

HBase Client 1.2.x 和 其他版本（如 1.1.x）存在接口变化，而 Table Store HBase Client 1.2.x 版本只能兼容 HBase Client 1.2.x。

如果需要使用 HBase Client 1.1.x 版本，请使用 Table Store HBase Client 1.1.x 版本。

如果需要使用 HBase Client 0.x.x 版本，请参考[迁移较早版本的 HBase](intl.zh-CN/产品功能/HBase 支持/迁移较早版本的 HBase.md#)。

## 配置文件 { .section}

从 HBase Client 迁移到 Table Store HBase Client，需要在配置文件中修改以下两点。

-   HBase Connection类型

    Connection 需要配置为 TableStoreConnection。

    ```language-java
        <property>
            <name>hbase.client.connection.impl</name>
            <value>com.alicloud.tablestore.hbase.TablestoreConnection</value>
        </property>
    
    ```

-   表格存储的配置项

    表格存储是云服务，提供了严格的权限管理。要访问表格存储，需要配置秘钥等信息。

    -   必须配置以下四个配置项才能成功访问表格存储：

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

    -   下面为可选配置项：

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

        -   hbase.client.tablestore.family 与 hbase.client.tablestore.family.$tablename

            -   表格存储只支持单列族，使用 HBase API 时，需要有一项 family 的内容，因此通过配置来填充此项 family 的内容。

                其中，`hbase.client.tablestore.family`为全局配置，`hbase.client.tablestore.family.$tablename`为单个表的配置。

            -   规则为：对表名为 T 的表，先找`hbase.client.tablestore.family.T`，如果不存在则找`hbase.client.tablestore.family`，如果依然不存在则取默认值 f。
        -   tablestore.client.max.connections

            最大链接数，默认是 300。

        -   tablestore.client.socket.timeout

            Socket 超时时间，默认是 15 秒。

        -   tablestore.client.connection.timeout

            连接超时时间，默认是 15 秒。

        -   tablestore.client.operation.timeout

            API 超时时间，默认是 Integer.MAX\_VALUE，类似于永不超时。

        -   tablestore.client.retries

            请求失败时，重试次数，默认是 3 次。


