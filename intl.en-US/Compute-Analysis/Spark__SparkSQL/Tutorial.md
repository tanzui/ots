# Tutorial {#concept_45019_zh .concept}

## Data preparation {#section_p19_3j0_cc4 .section}

Name a table in Table Store as pet and import the data. Note that column `name` is the only Primary Key.

|name|owner|species|sex|birth|death|
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

**Note:** As Table Store is schema-free , you do not need to input anything \(such as `NULL`\) into blank cells.

## Example for accessing by Spark SQL {#section_309_0fp_uag .section}

Preparations

Prepare the environment for Spark, JDK, and dependency package of Table Store SDK and EMR as [Prerequisites](reseller.en-US/Compute-Analysis/Spark__SparkSQL/Environment preparations.md#).

Examples

``` {#codeblock_bkw_awl_u6p}
$ bin/spark-sql --master local --jars tablestore-4.3.1-jar-with-dependencies.jar,emr-tablestore-1.4.2.jar
spark-sql> CREATE EXTERNAL TABLE pet
  (name STRING, owner STRING, species STRING, sex STRING, birth STRING, death STRING)
  STORED BY 'com.aliyun.openservices.tablestore.hive.TableStoreStorageHandler'
  WITH SERDEPROPERTIES(
    "tablestore.columns.mapping"="name,owner,species,sex,birth,death")
  TBLPROPERTIES (
    "tablestore.endpoint"="YourEndpoint",
    "tablestore.access_key_id"="YourAccessKeyId",
    "tablestore.access_key_secret"="YourAccessKeySecret",
    "tablestore.table.name"="pet");
spark-sql> SELECT * FROM pet;
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
spark-sql> SELECT * FROM pet WHERE birth > "1995-01-01";
Chirpy  Gwen    bird    f       1998-09-11      NULL
Puffball        Diane   hamster f       1999-03-30      NULL
Slim    Benny   snake   m       1996-04-29      NULL
Whistler        Gwen    bird    NULL    1997-12-09      NULL
Time taken: 1.41 seconds, Fetched 4 row(s)
			
```

Parameters explanation

-   WITH SERDEPROPERTIES
    -   tablestore.columns.mapping \(optional\): By default, the field names of the external tables \(written in lower case according to Hive conventions\) are the same as the column names \(names of Primary Key or Attribute columns\) in Table Store. However, due to case-sensitivity or charsets, the names may be different. In this case, tablestore.columns.mapping needs to be specified. This parameter is a comma-separated string. A blank space cannot be added before or after a comma. Each item is a column name and the order is the same as the field names of the external tables.

        **Note:** Table Store supports column names with blank characters. So a blank space is considered part of the column name.

-   TBLPROPERTIES
    -   tablestore.endpoint \(required\): The [endpoint](../../../../reseller.en-US/Developer Guide/Terms/Endpoint.md#). You can view the endpoint information of the instance on the Table Store console.

    -   tablestore.instance \(optional\): The [instance](../../../../reseller.en-US/Developer Guide/Terms/Instance.md#) name. If it is not specified, it is the first field of tablestore.endpoint.

    -   tablestore.table.name \(required\): The table name in Table Store.

    -   tablestore.access\_key\_id and tablestore.access\_key\_secret \(required\): See [Access control](https://partners-intl.aliyun.com/help/doc-detail/27296.htm).

    -   tablestore.sts\_token \(optional\): See [Security token](../../../../reseller.en-US/Developer Guide/Authorization management/RAM and STS.md#).


## Example for accessing by Spark {#section_yp4_ye6_86j .section}

The following example illustrates how to count rows in `pet` by Spark.

``` {#codeblock_lq3_5m4_2mm .language-java}
private static RangeRowQueryCriteria fetchCriteria() {
    RangeRowQueryCriteria res = new RangeRowQueryCriteria("YourTableName");
    res.setMaxVersions(1);
    List<PrimaryKeyColumn> lower = new ArrayList<PrimaryKeyColumn>();
    List<PrimaryKeyColumn> upper = new ArrayList<PrimaryKeyColumn>();
    lower.add(new PrimaryKeyColumn("YourPkeyName", PrimaryKeyValue.INF_MIN));
    upper.add(new PrimaryKeyColumn("YourPkeyName", PrimaryKeyValue.INF_MAX));
    res.setInclusiveStartPrimaryKey(new PrimaryKey(lower));
    res.setExclusiveEndPrimaryKey(new PrimaryKey(upper));
    return res;
}

public static void main(String[] args) {
    SparkConf sparkConf = new SparkConf().setAppName("RowCounter");
    JavaSparkContext sc = new JavaSparkContext(sparkConf);

    Configuration hadoopConf = new Configuration();
    TableStoreInputFormat.setCredential(
        hadoopConf,
        new Credential("YourAccessKeyId", "YourAccessKeySecret"));
    TableStoreInputFormat.setEndpoint(
        hadoopConf,
        new Endpoint("https://YourInstance.Region.ots.aliyuncs.com/"));
    TableStoreInputFormat.addCriteria(hadoopConf, fetchCriteria());

    try {
        JavaPairRDD<PrimaryKeyWritable, RowWritable> rdd = sc.newAPIHadoopRDD(
            hadoopConf,
            TableStoreInputFormat.class,
            PrimaryKeyWritable.class,
            RowWritable.class);
        System.out.println(
            new Formatter().format("TOTAL: %d", rdd.count()).toString());
    } finally {
        sc.close();
    }
}
			
```

**Note:** If you use scala, replace JavaSparkContext with SparkContext, and replace JavaPairRDD with PairRDD.

## Run the program {#section_db8_t1m_f7z .section}

``` {#codeblock_f6v_1k1_ggn .language-sh}
$ bin/spark-submit --master local --jars hadoop-connector.jar row-counter.jar
TOTAL: 9
			
```

## Data type conversion {#section_tr1_02x_5o0 .section}

Table Store and Hive/Spark support the different sets of data types.

The following table indicates data type conversion support from Table Store \(rows\) to Hive \(columns\).

| |TINYINT|SMALLINT|INT|BIGINT|FLOAT|DOUBLE|BOOLEAN|STRING|BINARY|
|:-|:------|:-------|:--|:-----|:----|:-----|:------|:-----|:-----|
|INTEGER|Yes, with limited precision|Yes, with limited precision|Yes, with limited precision|Yes|Yes, with limited precision|Yes, with limited precision| | | |
|DOUBLE|Yes, with limited precision|Yes, with limited precision|Yes, with limited precision|Yes, with limited precision|Yes, with limited precision|Yes| | | |
|BOOLEAN| | | | | | |Yes| | |
|STRING| | | | | | | |Yes| |
|BINARY| | | | | | | | |Yes|

