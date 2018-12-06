# Stream Client {#concept_57167_zh .concept}

You can use Table Store Stream APIs and Table Store SDKs to read Stream records. When you obtain incremental data in real time mode, note that information in shards is not static. Shards may be split or merged. When shards are changed, you must process the dependencies between them to make sure that data in a single primary key is read in sequence. In addition, if your data is generated concurrently from multiple clients, multiple consumers must concurrently read the incremental records in each shard to improve the efficiency of exporting incremental data.

Stream Client is used to resolve common problems during Stream data processing, for example, load balancing, fault recovery, checkpoint, and shard information synchronization to guarantee the information consumption sequence. After using Stream Client, you only need to focus on the processing logic of each record.

This topic describes the principles of Stream Client, and how to use Stream Client to efficiently build a data tunnel that is applicable to your own services.

## How Stream Client works {#section_pp3_nqm_cfb .section}

To easily implement job scheduling and record the read progress of each current shard, Stream Client uses a table of Table Store to record the information. You can customize the table name, but you must make sure that this table name is not used by other services.

Stream Client defines a lease for each shard, and the owner of each lease is called the worker. A lease is used to record the incremental data consumers \(that is, workers\) of the shard and read the progress. When a new consumer is started, the worker is initialized, checking the shard and lease information and creating a lease for a shard if the shard does not have one. When a new shard is generated from shard splitting or combination, Stream Client inserts a lease record into the table. The new record is grabbed and continuously processed by a worker of a Stream Client. If a new worker joins, load balancing is implemented to dispatch the record to the new worker.

The following table describes the schema of the lease record.

|Parameter|Description|
|:--------|:----------|
|Primary key StreamId|ID of the currently processed Stream.|
|Primary key StatusType|Key of the current lease.|
|Primary StatusValue|ID of the shard corresponding to the current lease.|
|Attribute Checkpoint|Location where Stream data is consumed in the current shard \(for user fault recovery\).|
|Attribute LeaseCounter|Optimistic lock. The owner of each lease continues to update the counter value. Lease renewal indicates that the current lease is continuously occupied.|
|Attribute LeaseOwner|Name of the worker that owns the current lease.|
|Attribute LeaseStealer|Worker to which the lease is to be moved during load balancing.|
|Attribute ParentShardIds|Parent shard of the current shard. When the worker is consuming the current shard, make sure that the Stream of the parent shard has been consumed.|

## Example { .section}

The following figure shows a typical distributed architecture of using Stream Client to consume incremental data.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20288/153959622011693_en-US.png)

In this figure, worker1 and worker2 are two consumers based on Stream Client, for example, programs started on the ECS. The data source constantly reads/writes a table in Table Store. In the initial stage, the table contains shards P1, P2, and P3. With the increase of the traffic and data volume, P2 is split into P4 and P5. In the initial stage, worker1 consumes data of P1, and worker2 consumes data of P2 and P3. After P2 is split, P4 will be allocated to worker1, and P5 will be allocated to worker2. However, Stream Client makes sure that data of P4 and P5 is consumed after consumption of record R5 of P2 is complete. If a new consumer worker3 is deployed at this time, a shard on worker2 may be dispatched to worker3, resulting in load balancing.

In the preceding scenario, Stream Client generates the following lease information in the table:

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20288/153959622011694_en-US.png)

The worker in Stream Client is the carrier of the consumed Stream data. Each shard is allocated to a worker \(lease owner\). The owner constantly renews the current shard lease through heartbeats, that is, by updating LeaseCounter. Generally, each Steam consumer has a worker. After the worker is initialized, it obtains information about the shard to be processed. At the same time, the worker maintains its own thread pool, and concurrently and cyclically pulls incremental data of each shard it owns. The worker initialization process is as follows:

1.  Reads the Table Store configuration and initializes the client that accesses Table Store through an intranet.
2.  Obtains the Stream information of the corresponding table and initializes the lease management class. The lease management class synchronizes the lease information and creates a new lease record for the new shard.
3.  Initializes the shard synchronization class, which maintain the heartbeats of the current owned shards.
4.  Cyclically obtains the incremental data of the shard currently owned by the worker.

## Download Stream Client { .section}

-   Download and install the [JAR package](https://oss.sonatype.org/service/local/artifact/maven/redirect?r=releases&g=com.aliyun.openservices&a=tablestore-streamclient&v=1.0.0&e=jar) 

-   Maven：

    ```language-POM
    <dependency>
      <groupId>com.aliyun.openservices</groupId>
      <artifactId>tablestore-streamclient</artifactId>
      <version>1.0.0</version>
    </dependency>
    
    ```

    **Note:** The code of Stream Client is open-sourced. You can [download the source code](https://github.com/aliyun/aliyun-tablestore-stream-client) to learn about the principle. You are also welcomed to share good Stream-based sample code with us.


## Use Stream Client APIs { .section}

Stream Client provides the `IRecordProcessor` API, facilitating you to use Stream Client to consume the Stream data and hide the shard read logic and dispatch logic. The worker of Stream Client calls the `processRecords` function after pulling the Steam data to trigger your data processing logic.

```language-Java
public interface IRecordProcessor {

    void initialize(InitializationInput initializationInput);

    void processRecords(ProcessRecordsInput processRecordsInput);

    void shutdown(ShutdownInput shutdownInput);
}

```

The parameters are described as follows:

|Parameter|Description|
|:--------|:----------|
|void initialize\(InitializationInput initializationInput\);|Used to initialize a read task. It indicates that Stream Client is about to read data of a shard.|
|void processRecords\(ProcessRecordsInput processRecordsInput\);|Indicates how the user wants to process this batch of records after the data is read. The `getCheckpointer` function in `ProcessRecordsInput` can be used to obtain `IRecordProcessorCheckpointer`. The framework provides this API to implement the checkpoint. You can determine how often the checkpoint is implemented.|
|void shutdown\(ShutdownInput shutdownInput\);|Used to end the read task of a shard.|

**Note:** 

-   The read tasks are implemented in different machines, the process may encounter various types of errors, for example, restart due to an environment factor. Therefore, you must periodically record the completed data \(checkpoint\). When a task is restarted, it is continued from the last checkpoint. In other words, Stream Client does not guarantee that a record is sent through `ProcessRecordsInput` only once. It only guarantees that the record is sent at least once, and that the record sequence does not change. If some data is repeatedly sent, you must pay attention to the service processing logic.

-   If you want to reduce the repeat data processing times in case of an error, you can increase the frequency of the checkpoint operation. However, too frequent checkpoints reduce the system throughput. Therefore, determine the checkpoint frequency based on your service features.

-   If you find that the incremental data fails to be consumed in time, you can increase resources for the consumer, such as using more nodes to read the Stream record.


The following provides a simple example to describe how to use Stream Client to obtain the incremental data in real time and output the incremental data on the console.

```language-StreamSample
public class StreamSample {
   class RecordProcessor implements IRecordProcessor {

       private long creationTime = System.currentTimeMillis();
       private String workerIdentifier;

       public RecordProcessor(String workerIdentifier) {
           this.workerIdentifier = workerIdentifier;
       }

       public void initialize(InitializationInput initializationInput) {
           // Trace some info before start the query like stream info etc.
       }

       public void processRecords(ProcessRecordsInput processRecordsInput) {
           List<StreamRecord> records = processRecordsInput.getRecords();

           if(records.size() == 0) {
               // No more records we can wait for the next query
               System.out.println("no more records");
           }
           for (int i = 0; i < records.size(); i++) {
               System.out.println("records:" + records.get(i));
           }
           
           // Since we don't persist the stream record we can skip blow step 
           System.out.println(processRecordsInput.getCheckpointer().getLargestPermittedCheckpointValue());
           try {
               processRecordsInput.getCheckpointer().checkpoint();
           } catch (ShutdownException e) {
               e.printStackTrace();
           } catch (StreamClientException e) {
               e.printStackTrace();
           } catch (DependencyException e) {
               e.printStackTrace();
           }
       }

       public void shutdown(ShutdownInput shutdownInput) {
           // finish the query task and trace the shutdown reason
           System.out.println(shutdownInput.getShutdownReason());
       }
   }

   class RecordProcessorFactory implements IRecordProcessorFactory {

       private final String workerIdentifier;

       public RecordProcessorFactory(String workerIdentifier) {
           this.workerIdentifier = workerIdentifier;
       }

       public IRecordProcessor createProcessor() {
           return new StreamSample.RecordProcessor(workerIdentifier);
       }
   }

   public Worker getNewWorker(String workerIdentifier) {
       // Please replace with your table info
       final String endPoint = "";
       final String accessId = "";
       final String accessKey = "";
       final String instanceName = "";

       StreamConfig streamConfig = new StreamConfig();
       streamConfig.setOTSClient(new SyncClient(endPoint, accessId, accessKey,
               instanceName));
       streamConfig.setDataTableName("teststream");
       streamConfig.setStatusTableName("statusTable");

       Worker worker = new Worker(workerIdentifier, new ClientConfig(), streamConfig,
               new StreamSample.RecordProcessorFactory(workerIdentifier), Executors.newCachedThreadPool(), null);
       return worker;
   }

   public static void main(String[] args) throws InterruptedException {
       StreamSample test = new StreamSample();
       Worker worker1 = test.getNewWorker("worker1");
       Thread thread1 = new Thread(worker1);
       thread1.start();
   }
}

```

