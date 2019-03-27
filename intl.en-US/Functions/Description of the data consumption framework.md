# Description of the data consumption framework {#concept_qcz_22v_vgb .concept}

Tunnel Service uses comprehensive operations of Table Store to consume full and incremental data. You can easily consume and process history data and incremental data in tables.

A Tunnel client is an automatic data consumption framework of Tunnel Service. The Tunnel client regularly checks heartbeats to detect active channels, update status of the Channel and ChannelConnect classes, initialize, run, and terminate data processing tasks.

The Tunnel client supports the following features for processing full and incremental data: load balancing, fault recovery, checkpoints, and partition information synchronization to ensure the sequence of consuming information. The Tunnel client allows you to focus on the processing logic of each record.

The following sections describe the features of the Tunnel client, including automatic data processing, load balancing, and fault tolerance. For more information, see [Github](https://github.com/aliyun/aliyun-tablestore-java-sdk) to check source code of the Tunnel client.

## Automatic data processing {#section_v4c_k2v_vgb .section}

The Tunnel client regularly checks for heartbeats to detect active channels, update status of the Channel and ChannelConnect classes, initialize, run, and terminate data processing tasks. This section describes the data processing logic. For more information, see source code.

1.  Initialize resources of the Tunnel client.
    1.  Change the status of the Tunnel client from Ready to Started.
    2.  Set the HeartbeatTimeout and ClientTag parameters in TunnelWorkerConfig to run the ConnectTunnel task and connect Tunnel Service to obtain the ClientId of the current Tunnel client.
    3.  Initialize the ChannelDialer class to create a ChannelConnect task. Each ChannelConnect class corresponds to a Channel class, and the ChannelConnect task records data consumption checkpoints.
    4.  Set the Callback parameter for processing data and the CheckpointInterval parameter for specifying the interval of outputting checkpoints in Tunnel Service. In this way, you can create a data processor that automatically outputs checkpoints.
    5.  Initialize the TunnelStateMachine class to automatically update the status of the Channel class.
2.  Regularly check heartbeat messages.

    You can set the heartbeatIntervalInSec parameter in TunnelWorkerConfig to set the interval for checking the heartbeat.

    1.  Send a heartbeat request to obtain the list of latest available channels from Tunnel Service. The list includes the ChannelId, channel versions, and channel status.
    2.  Merge the list of channels from Tunnel Service with the local list of channels, and create and update ChannelConnect tasks. Follow these rules:
        -   Merge: overwrite the earlier version in the local list with the later version for the same ChannelId from Tunnel Service, and insert the new channels from Tunnel Service into the local list.
        -   Create a ChannelConnect task: create a ChannelConnect task in WAIT status for a channel that has no ChannelConnect task. If the ChannelConnect task corresponds to a channel in OPEN status, run the ReadRecords&&ProcessRecords task that cyclically processes data for this ChannelConnect task. For more information, see the ProcessDataPipeline class in source code.
        -   Update an existing ChannelConnect task: after you merge the lists of channels, if a channel corresponds to a ChannelConnect task, update the ChannelConnect status according to the status of channels with the same ChannelId. For example, if channels are in Close status, set their ChannelConnect tasks to the Closed status to terminate the corresponding pipeline tasks. For more information, see the ChannelConnect.notifyStatus method in source code.
3.  Automatically process channel status.

    Based on the number of active Tunnel clients obtained in the heartbeat request, Tunnel Service allocates available partitions to different clients to balance the loads. Tunnel Service automatically processes channel status as described in the following figure, and drives channel consumption and load balancing.

    ![](images/39085_en-US.png)

    Tunnel Service and Tunnel clients change their status by using heartbeat requests and channel version updates.

    1.  Each channel is initially in WAIT status.
    2.  The channel for incremental data changes to the OPEN status only when the channel consumption on the parent partition is terminated.
    3.  Tunnel Service allocates the partition in OPEN status to each Tunnel client.
    4.  During load balancing, Tunnel Service and Tunnel clients use a scheduling protocol for changing a channel status from Open, Closing to Closed. After consuming a BaseData channel or a Stream channel, Tunnel clients report the channel as Terminated.

## Automatic load balancing and excellent horizontal scaling {#section_msd_x5v_vgb .section}

-   Multiple Tunnel clients can consume data by using the same Tunnel or TunnelId. When the Tunnel clients run the heartbeat task, Tunnel Service automatically redistributes channels and tries to allocate active channels to each Tunnel client to achieve load balancing.
-   You can easily add Tunnel clients to scale out. Tunnel clients can run on one or more instances.

## Automatic resource clearing and fault tolerance {#section_hpl_bwv_vgb .section}

-   Resource clearing: if Tunnel clients do not shut down normally, such as exceptional exit or manual termination, the system recycles resources automatically. For example, the system can release the thread pool, call the shutdown method that you have registered for the corresponding channel, and terminate the connection to Tunnel Service.
-   Fault tolerance: when a Tunnel client has non-parametric errors such as heartbeat timeout, the system automatically renews connections to continue stable data consumption.

