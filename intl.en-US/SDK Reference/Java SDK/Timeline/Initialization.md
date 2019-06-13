# Initialization {#concept_263744 .concept}

## Initialize the TimelineStore Factory {#section_7hh_0ou_2f7 .section}

You can use SyncClient as a parameter to initialize the TimelineStore Factory and create a Store that manages Meta data and Timeline data. The retry operation after an error occurs depends on the retry policy of SyncClient. You can set SyncClient for the retry. If you have any special requirements, you can implement the RetryStrategy operation to customize the policy.

``` {#codeblock_dn4_ziq_yas}
/**
 * Set the retry policy.
 * Code: configuration.setRetryStrategy(new DefaultRetryStrategy());
 * */
ClientConfiguration configuration = new ClientConfiguration();

SyncClient client = new SyncClient(
        "http://instanceName.cn-shanghai.ots.aliyuncs.com",
        "accessKeyId",
        "accessKeySecret",
        "instanceName", configuration);

TimelineStoreFactory factory = new TimelineStoreFactoryImpl(client);
```

## Initialize MetaStore {#section_x9y_aie_rps .section}

Create a schema for a Meta table. The schema includes parameters such as Identifier and MetaIndex. Create a Store that manages Meta data by using the TimelineStore Factory. You need to specify the following parameters: Meta table name, index, table name, primary key field, index name, and index type.

``` {#codeblock_9vs_6by_u09}
TimelineIdentifierSchema idSchema = new TimelineIdentifierSchema.Builder()
        .addStringField("timeline_id").build();

IndexSchema metaIndex = new IndexSchema();
metaIndex.addFieldSchema( //Configure the index field and index type.
        new FieldSchema("group_name", FieldType.TEXT).setIndex(true).setAnalyzer(FieldSchema.Analyzer.MaxWord)
        new FieldSchema("create_time", FieldType.Long).setIndex(true)
);

TimelineMetaSchema metaSchema = new TimelineMetaSchema("groupMeta", idSchema)
        .withIndex("metaIndex", metaIndex); //Set the index.

TimelineMetaStore timelineMetaStore = serviceFactory.createMetaStore(metaSchema);
```

 **Create a table** 

Create a table by using the parameters in metaSchema. Afterward, create and configure an index.

``` {#codeblock_5em_b8w_wvx}
timelineMetaStore.prepareTables();
```

 **Delete a table** 

If a table contains an index, delete the index before deleting the table from the Store.

``` {#codeblock_wm6_st7_okv}
timelineMetaStore.dropAllTables();
```

## Initialize TimelineStore {#section_97z_fxe_tpq .section}

Create a schema for a Timeline table. The schema includes parameters such as Identifier and TimelineIndex. Create a Store that manages Timeline data by using the TimelineStore Factory. You need to specify the following parameters: Timeline table name, index, table name, primary key field, index name, and index type.

The BatchStore operation improves the concurrency performance on the basis of DefaultTableStoreWriter of Table Store. You can set the number of concurrent threads in the thread pool.

``` {#codeblock_68m_52k_sbh}
TimelineIdentifierSchema idSchema = new TimelineIdentifierSchema.Builder()
        .addStringField("timeline_id").build();

IndexSchema timelineIndex = new IndexSchema();
timelineIndex.setFieldSchemas(Arrays.asList(//Configure the index field and index type.
        new FieldSchema("text", FieldType.TEXT).setIndex(true).setAnalyzer(FieldSchema.Analyzer.MaxWord),
        new FieldSchema("receivers", FieldType.KEYWORD).setIndex(true).setIsArray(true)
));

TimelineSchema timelineSchema = new TimelineSchema("timeline", idSchema)
        .autoGenerateSeqId() //Specify the auto-increment column as the method to generate the SequenceId value.
        .setCallbackExecuteThreads(5) //Set the number of initial threads of DefaultTableStoreWriter to 5.
        .withIndex("metaIndex", timelineIndex); //Set the index.

TimelineStore timelineStore = serviceFactory.createTimelineStore(timelineSchema);
```

 **Create a table** 

Create a table by using the parameters in TimelineSchema. Afterward, create and configure an index.

``` {#codeblock_cjc_idm_v5q}
timelineStore.prepareTables();
```

 **Delete a table** 

If a table contains an index, delete the index before deleting the table from the Store.

``` {#codeblock_4cp_pki_wmx}
timelineStore.dropAllTables();
```

