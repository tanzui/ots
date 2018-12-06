# Stream Client {#concept_57167_zh .concept}

基于 Table Store Stream API 以及 Table Store SDK，您可以使用 API 或者 SDK 读取 Stream 的记录。在实时获取增量数据的时候，需要注意分区的信息不是静态的。分区可能会分裂、合并。当分区发生变化后，需要处理分区之间的依赖关系以确保单主键上数据顺序读取。同时，如果您的数据是由多客户端并发生成，为了提高导出增量数据的效率，也需要有多消费端并行读取各个分区的增量记录。

Stream Client 可以解决 Stream 数据处理时的常见问题，例如，如何做负载均衡，故障恢复，Checkpoint，分区信息同步确保分区信息消费顺序等。使用 Stream Client 后，您只需要关心每条记录的处理逻辑即可。

下面内容将介绍 Stream Client 的原理，以及如何使用 Stream Client 高效打造适合自身业务的数据通道。

## Stream Client 原理 {#section_pp3_nqm_cfb .section}

为了方便做任务调度，以及记录当前每个分区的读取进度，Stream Client 使用了 Table Store 的一张表来记录这些信息，您可以自行选定表名，但是要确保该表名没有其他业务在使用。

Stream Client 中为每个分区定义了一个租约（lease），每个租约的拥有者叫做 worker。租约用来记录一个分区的增量数据消费者（即 worker）和读取进度信息。当一个新的消费端启动后，worker 会初始化，检查分区和租约信息，为没有相应租约的分区创建租约。当因为分裂或合并产生新的分区时，Stream Client 会在表中插入一条租约记录。新的记录会被某一个 Stream Client 的 worker 抢到并不断处理，如果有新的 worker 加入则可能会做负载均衡，调度至新 worker 处理。

租约记录的 schema 如下所示：

|参数|说明|
|:-|:-|
|主键 StreamId|当前处理 Stream 的 Id。|
|主键 StatusType|当前 lease 的 Key。|
|主键 StatusValue|当前 lease 对应的分区的 Id。|
|属性 Checkpoint|记录当前分区 Stream 数据消费的位置（用户故障恢复）|
|属性 LeaseCounter|表示乐观锁。每个 lease 的 owner 会持续更新这个 counter 值，用来续租表示继续占有当前的 lease。|
|属性 LeaseOwner|拥有当前租约的 worker 名。|
|属性 LeaseStealer|在负载均衡的时候，表示准备挪至哪个 worker。|
|属性 ParentShardIds|当前 shard 的父分区信息。在 worker 消费当前 shard 时，保证父分区的 stream 数据已经被消费。|

## 示例 { .section}

下图是典型的使用 Stream Client 消费增量数据的分布式架构：

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20288/153801102311693_zh-CN.png)

在这张图中，worker1 和 worker2 是两个基于 Stream Client 的消费端，例如在 ECS 上启动的进程。数据源不断地读写 Table Store 中的表，这张表初期有分区 P1、P2 和 P3。随着访问量和数据量的增大，分区 P2 发生了一次分裂，产生了 P4 和 P5。worker1 在初始阶段会消费 P1 的数据，worker2 消费 P2 和 p3 的数据，当 P2 发生分裂后，新的分区 P4 会被分配给 worker1，分区 P5 分配给 worker2。但 Stream Client 会确保 P2 的 R5 记录已经被消费完成后，再开始消费 P4 和 P5 的数据。如果这时部署了一个新的消费端 worker3，那可能发生的一件事情是 worker2 上的某个分区会被 worker3 调度走，由此产生负载均衡。

在上述场景下，Stream Client 会在表中生成如下租约信息：

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20288/153801102411694_zh-CN.png)

Stream Client 中的 worker 是抽象消费 Stream 数据的载体，每一个分区会被分配一个 worker 即 lease 的拥有者，拥有者会不断地通过心跳，即更新 LeaseCounter 来续约当前的分区租约，通常每一个 Stream 消费端会起一个 worker，worker 在完成初始化后获取当前需要处理的分区信息，同时 worker 会维护自己的线程池，并发地循环拉取所拥有的各分区的增量数据。worker 初始化流程如下：

1.  读取 Table Store 的配置，对内部访问 Table Store 的客户端进行初始化。
2.  获取对应表的 Stream 信息，并初始化租约管理类。租约管理类会同步租约信息，为新分区创建租约记录。
3.  初始化分区同步类，分区同步类会维持当前持有的分区心跳。
4.  循环获取当前 worker 持有的分区的增量数据。

## 下载 Stream Client { .section}

-   下载安装[JAR 包](https://oss.sonatype.org/service/local/artifact/maven/redirect?r=releases&g=com.aliyun.openservices&a=tablestore-streamclient&v=1.0.0&e=jar) 

-   Maven：

    ```language-POM
    <dependency>
      <groupId>com.aliyun.openservices</groupId>
      <artifactId>tablestore-streamclient</artifactId>
      <version>1.0.0</version>
    </dependency>
    
    ```

    **说明：** Stream Client 的代码是开源的，您可以[下载源码](https://github.com/aliyun/aliyun-tablestore-stream-client)了解原理，也欢迎您将基于 Stream 的优秀 Sample 代码分享给我们。


## 使用 Stream Client 接口 { .section}

为了方便您使用 Stream Client 消费 Stream 数据、隐藏分区读取逻辑和调度逻辑，Stream Client 提供了 `IRecordProcessor` 接口。Stream Client 的 worker 会在拉取到 stream 数据后调用 `processRecords` 函数来触发用户的处理数据逻辑。

```language-Java
public interface IRecordProcessor {

    void initialize(InitializationInput initializationInput);

    void processRecords(ProcessRecordsInput processRecordsInput);

    void shutdown(ShutdownInput shutdownInput);
}

```

参数解释如下：

|参数|说明|
|:-|:-|
|void initialize\(InitializationInput initializationInput\);|用于初始化一个读取任务，表示 stream client 准备开始读取某个 shard 的数据。|
|void processRecords\(ProcessRecordsInput processRecordsInput\);|表示具体读取到数据后用户希望如何处理这批记录。在 `ProcessRecordsInput` 中有一个 `getCheckpointer` 函数可以得到一个 `IRecordProcessorCheckpointer`。这个接口是框架提供给用户用来做 checkpoint 的接口，用户可以自行决定多久做一次 checkpoint。|
|void shutdown\(ShutdownInput shutdownInput\);|表示结束某个 shard 的读取任务。|

**说明：** 

-   因为读取任务所在机器不同，进程可能会遇到各种类型的错误，例如因为环境因素重启，需要定期对处理完的数据做记录（checkpoint）。当任务重启后，会接着上次的 checkpoint 继续往后做。也就是说，在极端情况下，Stream Client 不保证 `ProcessRecordsInput` 里传给您的记录只有一次，只会保证数据至少传一次，且记录的顺序不变。如果出现局部数据重复发送的情况，需要您注意业务的处理逻辑。

-   如果您希望减少在出错情况下数据的重复处理，可以增加做 checkpoint 的频率。但是过于频繁的 checkpoint 会降低系统的吞吐量，请根据自身业务特点决定做 checkpoint 的频率。

-   如果您发现增量数据不能及时被消费，可以增加消费端的资源，例如用更多的节点来读取 stream 记录。


下面给出了一个简单的示例，使用 Stream Client 来实时获取增量数据，并在控制台输出增量数据。

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

