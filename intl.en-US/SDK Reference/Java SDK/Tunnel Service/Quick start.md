# Quick start {#concept_ewh_qwv_vgb .concept}

This topic describes how to use the Java SDK to start Tunnel Service. Example code is attached to the end of this topic.

1.  Initialize a Tunnel client.

    ```
    // Set endPoint to the endpoint of the Table Store instance, such as https://instance.cn-hangzhou.ots.aliyuncs.com.
    // Set accessKeyId and accessKeySecret to the AccessKeyId and AccessKeySecret for accessing the Table Store service.
    // Set instanceName to the name of the target instance.
    final String endPoint = "";
    final String accessKeyId = "";
    final String accessKeySecret = "";
    final String instanceName = "";
    TunnelClient tunnelClient = new TunnelClient(endPoint, accessKeyId, accessKeySecret, instanceName);
    ```

2.  Create a tunnel.

    Create a testing table or prepare an existing table before creating the tunnel. To create a testing table, you can use the createTable method in the SyncClient class or go to the Table Store console.

    ```
    // You can create three types of tunnels: TunnelType.BaseData, TunnelType.Stream, and TunnelType.BaseAndStream.
    // The following example shows how to create a BaseAndStream tunnel. To create the other types of tunnels, set TunnelType in CreateTunnelRequest to the required type.
    final String tableName = "testTable";
    final String tunnelName = "testTunnel";
    CreateTunnelRequest request = new CreateTunnelRequest(tableName, tunnelName, TunnelType.BaseAndStream);
    CreateTunnelResponse resp = tunnelClient.createTunnel(request);
    // Use tunnelId to initialize TunnelWorker. You can call the ListTunnel or DescribeTunnel operation to obtain tunnelId.
    String tunnelId = resp.getTunnelId(); 
    System.out.println("Create Tunnel, Id: " + tunnelId);
    ```

3.  Customize the data consumption callback to start automatic data consumption.

    ```
    // Customize the data consumption callback or call the IChannelProcessor operation. Specify the process and shutdown methods.
    private static class SimpleProcessor implements IChannelProcessor {
        @Override
        public void process(ProcessRecordsInput input) {
            // ProcessRecordsInput includes the data that you have obtained.
            System.out.println("Default record processor, would print records count");
            System.out.println(
                String.format("Process %d records, NextToken: %s", input.getRecords().size(), input.getNextToken()));
            try {
                // Mock Record Process.
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        @Override
        public void shutdown() {
            System.out.println("Mock shutdown");
        }
    }
    
    // By default, TunnelWorkerConfig starts the thread pool for reading data and processing data. A single server starts multiple TunnelWorkers.
    // We recommend that you share the same TunnelWorkerConfig. TunnelWorkerConfig provides more advanced parameters.
    // For more information, see https://help.aliyun.com/document_detail/108058.html.
    TunnelWorkerConfig config = new TunnelWorkerConfig(new SimpleProcessor());
    // Configure TunnelWorker and start automatic data processing.
    TunnelWorker worker = new TunnelWorker(tunnelId, tunnelClient, config);
    try {
        worker.connectAndWorking();
    } catch (Exception e) {
        e.printStackTrace();
        worker.shutdown();
        tunnelClient.shutdown();
    }
    ```

4.  **Cautions** 
    -   By default, TunnelWorkerConfig starts the thread pool for reading data and processing data. A single server starts multiple TunnelWorkers. **We recommend that**you share the same TunnelWorkerConfig.
    -   When you create a tunnel for consuming full and incremental data, the tunnel retains incremental logs for a maximum of seven days. The specific expiration time of incremental logs is the same as that of incremental data for a table. If the tunnel does not complete the consumption of full data within seven days, the OTSTunnelExpired error occurs when the tunnel starts to consume incremental data. As a result, the tunnel cannot consume incremental data. If your estimate that the tunnel cannot complete the consumption of full data within seven days, submit a ticket or join DingTalk group 11789671 to request technical support.
    -   TunnelWorker requires warm-up time during initialization. The heartbeatIntervalInSec parameter in TunnelWorkerConfig determines the warm-up time. You can use the setHeartbeatIntervalInSec method in TunnelWorkerConfig to set this parameter. The default value is 30s and the minimum value is 5s. For more information, see [Description of the data consumption framework](../../../../intl.en-US/Functions/Description of the data consumption framework.md#) and [Configure the data consumption framework](intl.en-US/SDK Reference/Java SDK/Tunnel Service/Configure the data consumption framework.md#).
    -   The change from BaseData channels to Stream channels in the tunnel requires initialization. The heartbeatIntervalInSec parameter determines the initialization time.
    -   When shutting down abnormally, for example, an exceptional exit or manual termination, TunnelWorker automatically recycles resources in the following ways: 1. releases the thread pool. 2. automatically calls the shutdown method that you have registered for the Channel class. 3. disconnects the tunnel.
5.  Appendix: example

    ``` {#codeblock_63n_7pw_8bx}
    import com.alicloud.openservices.tablestore.TunnelClient;
    import com.alicloud.openservices.tablestore.TunnelClient;
    import com.alicloud.openservices.tablestore.model.tunnel.CreateTunnelRequest;
    import com.alicloud.openservices.tablestore.model.tunnel.CreateTunnelResponse;
    import com.alicloud.openservices.tablestore.model.tunnel.TunnelType;
    import com.alicloud.openservices.tablestore.tunnel.worker.IChannelProcessor;
    import com.alicloud.openservices.tablestore.tunnel.worker.ProcessRecordsInput;
    import com.alicloud.openservices.tablestore.tunnel.worker.TunnelWorker;
    import com.alicloud.openservices.tablestore.tunnel.worker.TunnelWorkerConfig;
    public class TunnelQuickStart {
        private static class SimpleProcessor implements IChannelProcessor {
            @Override
            public void process(ProcessRecordsInput input) {
                System.out.println("Default record processor, would print records count");
                System.out.println(
                    String.format("Process %d records, NextToken: %s", input.getRecords().size(), input.getNextToken()));
                try {
                    // Mock Record Process.
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            @Override
            public void shutdown() {
                System.out.println("Mock shutdown");
            }
        }
        public static void main() throws Exception {
            // 1. Initialize the Tunnel client.
            final String endPoint = "";
            final String accessKeyId = "";
            final String accessKeySecret = "";
            final String instanceName = "";
            TunnelClient tunnelClient = new TunnelClient(endPoint, accessKeyId, accessKeySecret, instanceName);
            // 2. Create a tunnel. You need to use the createTable method in SyncClient or go to the Table Store console to create a testing table in advance.
            final String tableName = "testTable";
            final String tunnelName = "testTunnel";
            CreateTunnelRequest request = new CreateTunnelRequest(tableName, tunnelName, TunnelType.BaseAndStream);
            CreateTunnelResponse resp = tunnelClient.createTunnel(request);
            // Use tunnelId to initialize TunnelWorker. You can call the ListTunnel or DescribeTunnel operation to obtain tunnelId.
            String tunnelId = resp.getTunnelId();
            System.out.println("Create Tunnel, Id: " + tunnelId);
            // 3. Customize the data consumption callback to start automatic data consumption.
            // TunnelWorkerConfig provides more advanced parameters. For more information, see the description in the related topic.
            TunnelWorkerConfig config = new TunnelWorkerConfig(new SimpleProcessor());
            TunnelWorker worker = new TunnelWorker(tunnelId, tunnelClient, config);
            try {
                worker.connectAndWorking();
            } catch (Exception e) {
                e.printStackTrace();
                worker.shutdown();
                tunnelClient.shutdown();
            }
        }
    }
    ```


