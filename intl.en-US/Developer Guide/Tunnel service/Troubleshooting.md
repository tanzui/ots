# Troubleshooting

This topic describes the format of error messages and error codes returned by Tunnel Service.

After Tunnel Service receives an abnormal request, Tunnel Service returns an HTTP status code and an error message in the Protobuf format.

## Error message format

A error message returned by Tunnel Service is in the following Protobuf format:

```
message Error {
    required string code = 1;
    optional string message = 2;
    optional string tunnel_id = 3;
}
```

## Error codes

When you use Tunnel Service SDKs, you need only to handle the error codes whose processing logic is "Return an error message". The SDKs automatically process other error codes and retry the requests. We recommend that you handle an error based on the processing logic of the error code.

|HTTP status code|Error code|Description|Processing logic|
|:---------------|:---------|:----------|:---------------|
|400|OTSParameterInvalid|The error message returned because an parameter in the API request is incorrect or the requested data table does not exist.|Return an error message.|
|400|OTSTunnelExpired|The error message returned because the log data in an incremental or a differential tunnel expires.|Return an error message.|
|403|OTSPermissionDenied|The error message returned because you are not authorized to access the specified resource.|Return an error message.|
|409|OTSTunnelExist|The error message returned because the tunnel that you want to create already exists on the server.|Return an error message.|
|400|OTSSequenceNumberNotMatch|The error message returned because the serial numbers of checkpoints are inconsistent between the client and server. This error can occur when the serial numbers of checkpoints on the client are smaller than those on the server or channels compete with each other.|Use the checkpoint API to obtain the serial numbers of checkpoints again.|
|410|OTSResourceGone|The error message returned because the request sent to obtain the heartbeat information about the Tunnel Service client times out.|Use the tunnel ID to reconnect to Tunnel Service.|
|503|OTSTunnelServerUnavailable|The error message returned because an internal server error occurs.|Use exponential backoff to retry the request.|

