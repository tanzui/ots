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
|TunnelRPO|The last time when the tunnel consumes incremental data. The time equals the point in time when the slowest channel in the tunnel consumes data. Default value: January 1, 1970 \(UTC\).|
|List<TunnelInfo\>|The list of the tunnel, including the following information: -   TunnelId: the ID of the tunnel.
-   TunnelName: the name of the tunnel.
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
-   ChannelRPO: the last time when a channel consumes incremental data. Default value: January 1, 1970 \(UTC\). This parameter is unavailable for the full data consumption. |
|ResponseInfo|Some other fields returned, including the RequestId field in the request. RequestId uniquely identifies the request. |

## Examples

```
req := &tunnel.DescribeTunnelRequest{
   TableName:  "testTable",
   TunnelName: "testTunnel",
}
resp, err := tunnelClient.DescribeTunnel(req)
if err != nil {
   log.Fatal("describe test tunnel failed", err)
}
log.Println("tunnel id is", resp.Tunnel.TunnelId)
```

