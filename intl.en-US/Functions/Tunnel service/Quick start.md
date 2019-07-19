# Quick start {#concept_mnl_5s3_kgb .concept}

You can use Tunnel Service in the Table Store console.

## Prerequisites {#section_zlv_358_pll .section}

You have [activated Table Store](intl.en-US/Quick Start/Sign up for Table Store.md#).

## Create a tunnel {#section_to8_id1_vyq .section}

1.  Log on to the [Table Store console](https://ots.console.aliyun.com).
2.  Locate the target table and click **Tunnels** in the Actions column.
3.  On the Tunnels page, click **Create Tunnel** in the upper-right corner.
4.  In the Create Tunnel dialog box that appears, set **Tunnel Name** and **Type**.

    Tunnel Service provides three types of real-time consumption tunnels for distributed data, including **Incremental**, **Full**, and **Differential**. You can set the type as required. This topic uses the **Incremental** type as an example.

    After the tunnel is created, you can check the data in the tunnel, monitor consumption latency, and check the number of consumed rows in each channel on the Tunnels page.


## Preview data types in a channel {#section_m6j_o7c_h4b .section}

1.  In the Table Store console, click Data Editor in the left-side navigation pane. On the Table Data page that appears, click Insert or Delete in the upper-right corner to write or delete data, respectively.
2.  Click Tunnels in the left-side navigation pane. On the Tunnels page that appears, locate the tunnel that you created and click **Show Channels** in the Actions column. The channels are listed at the bottom of the page.
3.  Locate the target channel and click **View Simulated Export Records** in the Actions column. In the dialog box that appears, click Start. The data types in the channel appear.

## Enable data consumption for a tunnel {#section_z52_1qv_kx8 .section}

1.  Copy a tunnel ID from the tunnel list.
2.  Use the Tunnel Service SDK in any programming language to enable data consumption for the tunnel.

    ``` {#codeblock_nh2_ptn_ac6}
    // Customize the data consumption callback, that is, implement the process and shutdown methods of the IChannelProcessor interface.
    private static class SimpleProcessor implements IChannelProcessor {
        @Override
        public void process(ProcessRecordsInput input) {
            System.out.println("Default record processor, would print records count");
            System.out.println(
                String.format("Process %d records, NextToken: %s", input.getRecords().size(), input.getNextToken()));
            try {
                // Mock record processing.
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
    
    // TunnelWorkerConfig contains more advanced parameters. For more information, see the description in the related topic.
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


## View data consumption logs {#section_g0e_6hc_sry .section}

You can view the consumption logs of incremental data in the data consumption standard output. You can also log on to the Table Store console or call the DescribeTunnel operation to view the consumption latency and the number of consumed rows in each channel.

