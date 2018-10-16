# Tutorial {#concept_45028_zh .concept}

## Data preparation { .section}

Name a table in Table Store as pet and import the following data. Note that column `Name` is the only Primary Key.

|Name|Owner|Species|Sex|Birth|Death|
|:---|:----|:------|:--|:----|:----|
|Fluffy|Harold|cat|f|1993-02-04| |
|Claws|Gwen|cat|m|1994-03-17| |
|Buffy|Harold|dog|f|1989-05-13| |
|Fang|Benny|dog|m|1990-08-27|
|Bowser|Diane|dog|m|1979-08-31|1995-07-29|
|Chirpy|Gwen|bird|f|1998-09-11| |
|Whistler|Gwen|bird| |1997-12-09| |
|Slim|Benny|snake|m|1996-04-29| |
|Puffball|Diane|hamster|f|1999-03-30|

**Note:** As Table Store is schemafree \(according to the [Data model](../../../../reseller.en-US/Data Models/Preface.md#) topic\), you do not need to input anything \(such as `NULL`\) into blank cells.

## Example for accessing by Hive { .section}

Preparations

Prepare the environment for Hadoop, Hive, JDK, and dependency package of Table Store SDK and EMR as [Prerequisites](reseller.en-US/Compute-Analysis/Hive__HadoopMR/Environment preparations.md#).

Example

```
# You can add HADOOP_HOME and HADOOP_CLASSPATH into /etc/profile
$ export HADOOP_HOME=${Your Hadoop Path}
$ export HADOOP_CLASSPATH=emr-tablestore-1.4.2.jar:tablestore-4.3.1-jar-with-dependencies.jar:joda-time-2.9.4.jar
$ bin/hive
hive> CREATE EXTERNAL TABLE pet
  (name STRING, owner STRING, species STRING, sex STRING, birth STRING, death STRING)
  STORED BY 'com.aliyun.openservices.tablestore.hive.TableStoreStorageHandler'
  WITH SERDEPROPERTIES(
    "tablestore.columns.mapping"="name,owner,species,sex,birth,death")
  TBLPROPERTIES (
    "tablestore.endpoint"="YourEndpoint",
    "tablestore.access_key_id"="YourAccessKeyId",
    "tablestore.access_key_secret"="YourAccessKeySecret",
    "tablestore.table.name"="pet");
hive> SELECT * FROM pet;
Bowser  Diane   dog     m       1979-08-31      1995-07-29
Buffy   Harold  dog     f       1989-05-13      NULL
Chirpy  Gwen    bird    f       1998-09-11      NULL
Claws   Gwen    cat     m       1994-03-17      NULL
Fang    Benny   dog     m       1990-08-27      NULL
Fluffy  Harold  cat     f       1993-02-04      NULL
Puffball        Diane   hamster f       1999-03-30      NULL
Slim    Benny   snake   m       1996-04-29      NULL
Whistler        Gwen    bird    NULL    1997-12-09      NULL
Time taken: 5.045 seconds, Fetched 9 row(s)
hive> SELECT * FROM pet WHERE birth > "1995-01-01";
Chirpy  Gwen    bird    f       1998-09-11      NULL
Puffball        Diane   hamster f       1999-03-30      NULL
Slim    Benny   snake   m       1996-04-29      NULL
Whistler        Gwen    bird    NULL    1997-12-09      NULL
Time taken: 1.41 seconds, Fetched 4 row(s)

```

Parameters explanation

-   WITH SERDEPROPERTIES

    tablestore.columns.mapping \(optional\): By default, the field names of the external tables \(written in lower case according to Hive conventions\) are the same as the column names \(names of Primary Key or Attribute columns\) in Table Store. However, due to case-sensitivity or charsets, the names may be different. In this case, tablestore.columns.mapping needs to be specified. This parameter is a comma-separated string. A blank space cannot be added before or after a comma. Each item is a column name and the order is the same as the field names of the external tables.

    **Note:** Table Store supports column names with blank characters, which means a blank space is considered part of the column name.

-   TBLPROPERTIES
    -   tablestore.endpoint \(required\): The [endpoint](../../../../reseller.en-US/Product Introduction/Terms/Endpoint.md#). You can view the endpoint information of the instance on the Table Store console.

    -   tablestore.instance \(optional\): The [instance](../../../../reseller.en-US/Product Introduction/Terms/Instance.md#) name. If it is not specified, it is the first field of tablestore.endpoint.

    -   tablestore.table.name \(required\): The table name in Table Store.

    -   tablestore.access\_key\_id, tablestore.access\_key\_secret \(required\): See [Access control](https://partners-intl.aliyun.com/help/doc-detail/27296.htm).

    -   tablestore.sts\_token \(optional\): See [Security Token](../../../../reseller.en-US/Authorization/RAM and STS.md#).


## Example for accessing by HadoopMR { .section}

The following example illustrates how to count rows in pet using HadoopMR.

Code examples

-   Construct Mappers and Reducers.

    ```
    public class RowCounter {
    public static class RowCounterMapper
    extends Mapper<PrimaryKeyWritable, RowWritable, Text, LongWritable> {
        private final static Text agg = new Text("TOTAL");
        private final static LongWritable one = new LongWritable(1);
    
        @Override
        public void map(
            PrimaryKeyWritable key, RowWritable value, Context context)
            throws IOException, InterruptedException {
            context.write(agg, one);
        }
    }
    
    public static class IntSumReducer
    extends Reducer<Text,LongWritable,Text,LongWritable> {
    
        @Override
        public void reduce(
            Text key, Iterable<LongWritable> values, Context context)
            throws IOException, InterruptedException {
            long sum = 0;
            for (LongWritable val : values) {
                sum += val.get();
            }
            context.write(key, new LongWritable(sum));
        }
    }
    }
    
    ```

    Each time HadoopMR fetches a row from pet, it calls map\(\) of the mapper. The first two parameters, PrimaryKeyWritable and RowWritable, correspond to the row’s Primary Key and the contents of this row, respectively. You can get the Primary Key object and the row object defined by Table Store JAVA SDK by invoking PrimaryKeyWritable.getPrimaryKey\(\) and RowWritable.getRow\(\).

-   Configure Table Store as data source of mapper.

    ```
    	private static RangeRowQueryCriteria fetchCriteria() {
    	    RangeRowQueryCriteria res = new 	RangeRowQueryCriteria("YourTableName");
    	    res.setMaxVersions(1);
    	    List<PrimaryKeyColumn> lower = new ArrayList<PrimaryKeyColumn>();
    	    List<PrimaryKeyColumn> upper = new ArrayList<PrimaryKeyColumn>();
    	    lower.add(new PrimaryKeyColumn("YourPkeyName", PrimaryKeyValue.INF_MIN));
    	    upper.add(new PrimaryKeyColumn("YourPkeyName", PrimaryKeyValue.INF_MAX));
    	    res.setInclusiveStartPrimaryKey(new PrimaryKey(lower));
    	    res.setExclusiveEndPrimaryKey(new PrimaryKey(upper));
    	    return res;
    	}
    
    	public static void main(String[] args) throws Exception {
    	    Configuration conf = new Configuration();
    	    Job job = Job.getInstance(conf, "row count");
    	    job.addFileToClassPath(new Path("hadoop-connector.jar"));
    	    job.setJarByClass(RowCounter.class);
    	    job.setMapperClass(RowCounterMapper.class);
    	    job.setCombinerClass(IntSumReducer.class);
    	    job.setReducerClass(IntSumReducer.class);
    	    job.setOutputKeyClass(Text.class);
    	    job.setOutputValueClass(LongWritable.class);
    	    job.setInputFormatClass(TableStoreInputFormat.class);
    	    TableStoreInputFormat.setEndpoint(job, "https://YourInstance.Region.ots.aliyuncs.com/");
    	    TableStoreInputFormat.setCredential(job, "YourAccessKeyId", "YourAccessKeySecret");
    	    TableStoreInputFormat.addCriteria(job, fetchCriteria());
    	    FileOutputFormat.setOutputPath(job, new Path("output"));
    	    System.exit(job.waitForCompletion(true) ? 0 : 1);
    	}
    
    ```

    In the preceding example, job.setInputFormatClass\(TableStoreInputFormat.class\) is used to set Table Store as the data source. To complete the example, the following steps are also required:

    -   Deploy hadoop-connector.jar to the cluster and add it to classpath. The local path of hadoop-connector.jar is specified by addFileToClassPath\(\). In the code example, is assumes that hadoop-connector.jar is in the current path.

    -   Specify the endpoint and Access Key when accessing Table Store. They can be set using TableStoreInputFormat.setEndpoint\(\) and TableStoreInputFormat.setCredential\(\).

    -   Specify a table to count.

        **Note:** 

        -   TableStoreInputFormat.addCriteria\(\) can be invoked multiple times. Each invocation adds a RangeRowQueryCriteria object.
        -   Set setFilter\(\) and addColumnsToGet\(\) to filter unrequired rows and columns in server-side to reduce the cost and improve the performance of Table Store.
        -   Add RangeRowQueryCriterias to multiple tables to merge them.
        -   Add multiple RangeRowQueryCriterias to a single table to tune the splits. TableStore-Hadoop Connector can then split the range of the user’s input based on specified requirements.

## Run the program { .section}

```
$ HADOOP_CLASSPATH=hadoop-connector.jar bin/hadoop jar row-counter.jar
...
$ find output -type f
output/_SUCCESS
output/part-r-00000
output/. _SUCCESS.crc
output/.part-r-00000.crc
$ cat out/part-r-00000
TOTAL   9

```

## Data type conversion { .section}

Table Store and Hive/Spark support different sets of data types.

The following table indicates data type conversion support from Table Store \(rows\) to Hive \(columns\).

| |TINYINT|SMALLINT|INT|BIGINT|FLOAT|DOUBLE|BOOLEAN|STRING|BINARY|
|:-|:------|:-------|:--|:-----|:----|:-----|:------|:-----|:-----|
|INTEGER|Yes, with limited precision|Yes, with limited precision|Yes, with limited precision|Yes|Yes, with limited precision|Yes, with limited precision| | | |
|DOUBLE|Yes, with limited precision|Yes, with limited precision|Yes, with limited precision|Yes, with limited precision|Yes, with limited precision|Yes| | | |
|BOOLEAN| | | | | | |Yes| | |
|STRING| | | | | | | |Yes| |
|BINARY| | | | | | | | |Yes|

