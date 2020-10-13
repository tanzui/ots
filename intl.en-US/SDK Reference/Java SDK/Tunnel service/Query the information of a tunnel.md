# Query the information of a tunnel

You can call the DescribeTunnel operation to obtain the information of channels in a specified tunnel. A channel corresponds to a shard for the Stream operation of Tablestore.

## Request parameters

|Parameter|Description|
|---------|-----------|
|TableName|The name of the table for which you want to query the tunnel information.|
|TunnelName|The tunnel name.|

## Response parameters

|Parameter|Description|
|---------|-----------|
|TunnelConsumePoint|The last time when the tunnel consumes incremental data. The time equals the point in time when the slowest channel in the tunnel consumes data. Default value: January 1, 1970 \(UTC\).|
|TunnelInfo|The list of the tunnel, including the following information: -   TunnelId: the ID of the tunnel.
-   TunnelType: the type of the tunnel. Valid values: BaseData, Stream, and BaseAndStream.
-   TableName: the name of the table for which the tunnel is configured.
-   InstanceName: the name of the instance for which the tunnel is configured.
-   Stage: the stage at which the tunnel is located. Valid values: InitBaseDataAndStreamShard, ProcessBaseData, and ProcessStream.
-   Expired: indicates whether the data is expired.

If true is returned, contact Tablestore technical support by using DingTalk in a timely manner. |
|List<ChannelInfo\>|The list of channels in the tunnel, including the following information: -   ChannelId: the ID of the channel.
-   ChannelType: the type of the channel. Valid values: Base and Stream.
-   ChannelStatus: the status of the channel. Valid values: WAIT, OPEN, CLOSING, CLOSE, and TERMINATED.
-   ClientId: the ID of the tunnel client. By default, the client ID concatenates the hostname of the client and a random string. The client hostname can be customized by TunnelWorkerConfig.
-   ChannelConsumePoint: the last time when a channel consumes incremental data. Default value: January 1, 1970 \(UTC\). This parameter is unavailable for the full data consumption.
-   ChannelCount: the number of data entries that a channel synchronizes. |
|ResponseInfo|Some other fields returned in the request.|
|RequestId|The ID of the request.|

## Examples

```
// The ConsumePoint and recovery point objective (RPO) attributes are used for incremental data consumption, rather than for full data consumption.
// Stream tunnel: The Stage parameter in TunnelInfo is set to ProcessStream. Stream channel: The ChannelType parameter in ChannelInfo is set to Stream.
private static void describeTunnel(TunnelClient client, String tableName, String tunnelName) {
    DescribeTunnelRequest request = new DescribeTunnelRequest(tableName, tunnelName);
    DescribeTunnelResponse resp = client.describeTunnel(request);
    System.out.println("RequestId: " + resp.getRequestId());
    // The last time when the tunnel consumes incremental data. The time equals the point in time when the slowest channel in the tunnel consumes data. Default value: January 1, 1970 (UTC).
    System.out.println("TunnelConsumePoint: " + resp.getTunnelConsumePoint());
    System.out.println("TunnelInfo: " + resp.getTunnelInfo());
    for (ChannelInfo ci : resp.getChannelInfos()) {
        System.out.println("ChannelInfo::::::");
        System.out.println("\tChannelId: " + ci.getChannelId());
        // The type of a channel. Valid values: BaseData and Stream.
        System.out.println("\tChannelType: " + ci.getChannelType());
        // The ID of the tunnel client. By default, the client ID concatenates the hostname of the client and a random string.
        System.out.println("\tClientId: " + ci.getClientId());
        // The last time when the channel consumes incremental data.
        System.out.println("\tChannelConsumePoint: " + ci.getChannelConsumePoint());
        // The number of data entries that the channel synchronizes.
        System.out.println("\tChannelCount: " + ci.getChannelCount());
    }
}
```

