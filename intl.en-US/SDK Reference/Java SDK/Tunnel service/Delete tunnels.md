# Delete tunnels

You can call the DeleteTunnel operation to delete a tunnel for a table by specifying the name of the table and tunnel.

## Request parameters

|Parameter|Description|
|---------|-----------|
|TableName|The name of the table for which you want to delete the tunnel.|
|TunnelName|The tunnel name.|

## Response parameters

|Parameter|Description|
|---------|-----------|
|ResponseInfo|Some other fields returned.|
|RequestId|The ID of the request.|

## Examples

```
private static void deleteTunnel(TunnelClient client, String tableName, String tunnelName) {
    DeleteTunnelRequest request = new DeleteTunnelRequest(tableName, tunnelName);
    DeleteTunnelResponse resp = client.deleteTunnel(request);
    System.out.println("RequestId: " + resp.getRequestId());
}
```

