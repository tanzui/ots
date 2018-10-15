# Hello World {#concept_50163_zh .concept}

This topic describes how to use Table Store HBase Client to implement a simple Hello World program, and includes the following operations:

-   Configure project dependencies.
-   Connect Table Store
-   Create a table
-   Write Data
-   Read Data
-   Scan data
-   Delete a table

## Code position {#section_hd1_1kn_cfb .section}

This sample program uses HBase APIs to access Table Store. The complete sample program is located in the [Github aliyun-tablestore-hbase-client](https://github.com/aliyun/aliyun-tablestore-hbase-client) project. The directory is src/test/java/samples/HelloWorld.java.

## Use HBase APIs { .section}

-   Configure project dependencies

    Configure Maven dependencies as follows.

    ```language-xml
       <dependencies>
            <dependency>
                <groupId>com.aliyun.openservices</groupId>
                <artifactId>tablestore-hbase-client</artifactId>
                <version>1.2.0</version>
            </dependency>
        </dependencies>
    
    ```

    For more information about advanced configurations, see [Migrate from HBase to Table Store](reseller.en-US/Functions/HBase/Migrate from HBase to Table Store.md#).

-   Configure the file

    Add the following configuration items to hbase-site.xml.

    ```language-xml
    <configuration>
        <property>
            <name>hbase.client.connection.impl</name>
            <value>com.alicloud.tablestore.hbase.TablestoreConnection</value>
        </property>
        <property>
            <name>tablestore.client.endpoint</name>
            <value>endpoint</value>
        </property>
        <property>
            <name>tablestore.client.instancename</name>
            <value>instance_name</value>
        </property>
        <property>
            <name>tablestore.client.accesskeyid</name>
            <value>access_key_id</value>
        </property>
        <property>
            <name>tablestore.client.accesskeysecret</name>
            <value>access_key_secret</value>
        </property>
        <property>
            <name>hbase.client.tablestore.family</name>
            <value>f1</value>
        </property>
        <property>
            <name>hbase.client.tablestore.table</name>
            <value>ots_adaptor</value>
        </property>
    </configuration>
    
    ```

    For more information about advanced configurations, see [Migrate from HBase to Table Store](reseller.en-US/Functions/HBase/Migrate from HBase to Table Store.md#).

-   Connect Table Store

    Create a TableStoreConnection object to connect Table Store.

    ```language-java
    		Configuration config = HBaseConfiguration.create();
    		
    		// Create a Tablestore Connection
    		Connection connection = ConnectionFactory.createConnection(config);
    		
    		// Admin is used for creation, management, and deletion
            Admin admin = connection.getAdmin();
    
    ```

-   Create a table

    Create a table using the specified table name. Use the default table name for MaxVersions and TimeToLive.

    ```language-java
    		// Create an HTableDescriptor, which contains only one ColumnFamily
    		HTableDescriptor descriptor = new HTableDescriptor(TableName.valueOf(TABLE_NAME));
    		
    		// Create a ColumnFamily. Use the default ColumnFamily name for Max Versions and TimeToLive. The default ColumnFamily name for Max Versions is 1 and for TimeToLive is Integer.INF_MAX
    		descriptor.addFamily(new HColumnDescriptor(COLUMN_FAMILY_NAME));
    		
    		// Use the createTable API of the Admin to create a table
    		System.out.println("Create table " + descriptor.getNameAsString());
    		admin.createTable(descriptor);
    
    ```

-   Write Data

    Write a row of data to Table Store.

    ```language-java
    		// Create a TablestoreTable for reading, writing, updating, deletion, and other operations on a single table
    		Table table = connection.getTable(TableName.valueOf(TABLE_NAME));
    		
    		// Create a Put object with the primary key row_1
    		System.out.println("Write one row to the table");
            Put put = new Put(ROW_KEY);
    		
    		// Add a column. Table Store supports only single ColumnFamilies. The ColumnFamily name is configured in hbase-site.xml. If the ColumnFamily name is not configured, the default name is "f". In this case, the value of COLUMN_FAMILY_NAME may be null when data is written.
    		put.addColumn(COLUMN_FAMILY_NAME, COLUMN_NAME, COLUMN_VALUE);
    		
    		// Run put for Table, and use HBase APIs to write the row of data to Table Store
            table.put(put);
    
    ```

-   Read Data

    Read data of the specified row.

    ```language-java
    		// Create a Get object to read the row whose primary key is ROW_KEY.
    		Result getResult = table.get(new Get(ROW_KEY));
    		Result result = table.get(get);
    		
    		// Print the results
    		String value = Bytes.toString(getResult.getValue(COLUMN_FAMILY_NAME, COLUMN_NAME));
    		System.out.println("Get one row by row key");
    		System.out.printf("\t%s = %s\n", Bytes.toString(ROW_KEY), value);
    
    ```

-   Scan data

    Read data in the specified range.

    ```language-java
    	Scan data of all rows in the table
    	System.out.println("Scan for all rows:");
    	Scan scan = new Scan();
    
    	ResultScanner scanner = table.getScanner(scan);
    	
    	// Print the results cyclically
    	for (Result row : scanner) {
    		byte[] valueBytes = row.getValue(COLUMN_FAMILY_NAME, COLUMN_NAME);
    		System.out.println('\t' + Bytes.toString(valueBytes));
    	}
    
    ```

-   Delete a table

    Use Admin APIs to delete a table.

    ```language-java
    		print("Delete the table");
    		admin.disableTable(table.getName());
    		admin.deleteTable(table.getName());
    
    ```


## Complete code { .section}

```language-java
package samples;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.HColumnDescriptor;
import org.apache.hadoop.hbase.HTableDescriptor;
import org.apache.hadoop.hbase.TableName;
import org.apache.hadoop.hbase.client.*;
import org.apache.hadoop.hbase.util.Bytes;

import java.io.IOException;

public class HelloWorld {

    private static final byte[] TABLE_NAME = Bytes.toBytes("HelloTablestore");
    private static final byte[] ROW_KEY = Bytes.toBytes("row_1");
    private static final byte[] COLUMN_FAMILY_NAME = Bytes.toBytes("f");
    private static final byte[] COLUMN_NAME = Bytes.toBytes("col_1");
    private static final byte[] COLUMN_VALUE = Bytes.toBytes("col_value");

    public static void main(String[] args) {
        helloWorld();
    }

    private static void helloWorld() {

        try  {
            Configuration config = HBaseConfiguration.create();
            Connection connection = ConnectionFactory.createConnection(config);
            Admin admin = connection.getAdmin();

            HTableDescriptor descriptor = new HTableDescriptor(TableName.valueOf(TABLE_NAME));
            descriptor.addFamily(new HColumnDescriptor(COLUMN_FAMILY_NAME));

            System.out.println("Create table " + descriptor.getNameAsString());
            admin.createTable(descriptor);

            Table table = connection.getTable(TableName.valueOf(TABLE_NAME));

            System.out.println("Write one row to the table");
            Put put = new Put(ROW_KEY);
            put.addColumn(COLUMN_FAMILY_NAME, COLUMN_NAME, COLUMN_VALUE);
            table.put(put);

            Result getResult = table.get(new Get(ROW_KEY));
            String value = Bytes.toString(getResult.getValue(COLUMN_FAMILY_NAME, COLUMN_NAME));
            System.out.println("Get a one row by row key");
            System.out.printf("\t%s = %s\n", Bytes.toString(ROW_KEY), value);

            Scan scan = new Scan();

            System.out.println("Scan for all rows:");
            ResultScanner scanner = table.getScanner(scan);
            for (Result row : scanner) {
                byte[] valueBytes = row.getValue(COLUMN_FAMILY_NAME, COLUMN_NAME);
                System.out.println('\t' + Bytes.toString(valueBytes));
            }

            System.out.println("Delete the table");
            admin.disableTable(table.getName());
            admin.deleteTable(table.getName());

            table.close();
            admin.close();
            connection.close();
        } catch (IOException e) {
            System.err.println("Exception while running HelloTablestore: " + e.toString());
            System.exit(1);
        }
    }
}

```

