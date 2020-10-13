# Quick start

You can use Tunnel Service in the Tablestore console.

## Prerequisites

Tablestore is activated. For more information, see [Activate Tablestore](/intl.en-US/Quick Start/Activate Tablestore.md).

## Create a tunnel

1.  Log on to the [Tablestore console](https://otsnext.console.aliyun.com/).

2.  On the Overview page, click the name of the target instance or click **Manage Instance** in the Actions column.

3.  In the Tables section of the Instance Details tab, click the name of the target table and click the Tunnels tab. You can also click ![fig_001](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/7506659951/p100545.png) in the Actions column and select **Tunnels**.

4.  On the Tunnels tab, click **Create Tunnel**.

5.  In the Create Tunnel dialog box that appears, set **Tunnel Name** and **Type**.

    Tunnel Service provides three types of real-time consumption tunnels for distributed data, including **Incremental**, **Full**, and **Differential**. **Incremental** is selected in this topic as an example.

    After the tunnel is created, you can click **Show Channels** in the Actions column to check the data in the tunnel, monitor consumption latency, and check the number of consumed rows in each channel.

    ![fig_tunnel_service](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5047652061/p127068.png)


## Preview data types in a tunnel

After you create a tunnel, you can simulate data consumption to preview the data types in the tunnel.

1.  For more information about how to write data to or delete data from tables in the console, see [Read and write data in the console](/intl.en-US/Quick Start/Read and write data in the console.md).

2.  Preview the data types in a tunnel

    1.  On the Overview page, click the name of the target instance or click **Manage Instance** in the Actions column.

    2.  In the Tables section of the Instance Details tab, click the name of the target table and click the Tunnels tab. You can also click ![fig_001](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/7506659951/p100545.png) in the Actions column and select **Tunnels**.

    3.  On the Tunnels tab, click **Show Channels** in the Actions column.

    4.  Click **View Simulated Export Records** in the Actions column.

    5.  In the View Simulated Export Records dialog box that appears, click **Start**.

        The information about consumed data is displayed, as shown in the following figure.

        ![fig_consume_001](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/en-US/5047652061/p127073.png)


## Enable data consumption for a tunnel

1.  Copy a tunnel ID from the tunnel list.
2.  Use Tunnel Service SDK in any programming language to enable data consumption for the tunnel.

    ```
    // Customize the data consumption callback or call the IChannelProcessor operation. Specify the process and shutdown methods.
    private static class SimpleProcessor implements IChannelProcessor {
        @Override
        public void process(ProcessRecordsInput input) {
            System.out.println("Default record processor, would print records count");
            System.out.println(
                String.format("Process %d records, NextToken: %s", input.getRecords().size(), input.getNextToken()));
            try {
                // Simulate the processing of data consumption.
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
    
    // We recommend that you share the same TunnelWorkerConfig. TunnelWorkerConfig provides more advanced parameters.
    TunnelWorkerConfig config = new TunnelWorkerConfig(new SimpleProcessor());
    // Configure TunnelWorker and start automatic data processing.
    TunnelWorker worker = new TunnelWorker($tunnelId, tunnelClient, config);
    try {
        worker.connectAndWorking();
    } catch (Exception e) {
        e.printStackTrace();
        worker.shutdown();
        tunnelClient.shutdown();
    }
    ```


## View data consumption logs

After data is consumed, you can view the consumption logs of incremental data, including consumption statistics and the latest synchronization time of incremental data in channels. You can also log on to the Tablestore console or call the DescribeTunnel operation to view the consumption latency and the number of consumed rows in each channel.

