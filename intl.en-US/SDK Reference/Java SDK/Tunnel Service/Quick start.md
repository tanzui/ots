# Quick start {#concept_ewh_qwv_vgb .concept}

This topic describes how to use the Java SDK to quickly start Tunnel Service.

Follow these steps:

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
        // ProcessRecordsInput includes the data that you have obtained.
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


## Description {#section_gfz_hcw_vgb .section}

-   TunnelWorker requires warm-up time during initialization. The heartbeatIntervalInSec parameter in TunnelWorkerConfig determines the warm-up time. You can use the setHeartbeatIntervalInSec method in TunnelWorkerConfig to set this parameter. The default value is 30s and the minimum is 5s. For more information, see [Description of the data consumption framework](../../../../../reseller.en-US//Description of the data consumption framework.md#) and [Configure the data consumption framework](reseller.en-US/SDK Reference/Java SDK/Tunnel Service/Configure the data consumption framework.md#).
-   The change from BaseData channels to Stream channels in the tunnel requires initialization. The heartbeatIntervalInSec parameter determines the initialization time.
-   When shutting down abnormally, for example, an exceptional exit or manual termination, TunnelWorker automatically recycles resources with the following methods:
    -   Releases the thread pool.
    -   Automatically calls the shutdown method that you have registered for the Channel class.
    -   Disconnect the tunnel.

## Code example {#section_lnb_kcw_vgb .section}

The code example is as follows:

```
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

