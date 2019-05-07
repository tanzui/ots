# Troubleshooting {#concept_vjp_h2j_kgb .concept}

## Format definition {#section_tbw_3h4_kgb .section}

After Tunnel Service receives an abnormal request from a user, Tunnel Service returns the error message of an incorrect Protobuf format and an HTTP status code. The incorrect Protobuf format is as follows:

```
message Error {
    required string code = 1;
    optional string message = 2;
    optional string tunnel_id = 3;
}
```

## Error codes {#section_ubw_3h4_kgb .section}

Users of Table Store Tunnel Service Go SDKs need only to pay attention to error codes with the processing logic of "Returns an error message." Go SDKs of Table Store Tunnel Service automatically process the errors with other error codes and retry requests. We recommend that users of Table Store Tunnel Service APIs handle errors based on the processing logic.

|HTTP status code|Table Store error code|Description |Processing logic|
|:---------------|:---------------------|:-----------|:---------------|
|400|OTSParameterInvalid|The error message returned when an API request parameter is incorrect or no such data table exists.|Returns an error message.|
|400|OTSTunnelExpired|The error message returned when the log data in an incremental or a differential tunnel expires.|Returns an error message.|
|403|OTSPermissionDenied|The error message returned when you are not authorized to access the specified resource.|Returns an error message.|
|409|OTSTunnelExist|The error message returned when the tunnel to be created already exists on the server.|Returns an error message.|
|400|OTSSequenceNumberNotMatch|The error message returned when the serial numbers of checkpoints are inconsistent between the client and server. This error can occur when the serials numbers of checkpoints on the client are smaller than those on the server or channels compete with each other.|Use the checkpoint API to obtain the serial numbers of checkpoints again.|
|410|OTSResourceGone|The error message returned when a request sent to obtain the heartbeat information of the Tunnel Service client times out.|Use the tunnel ID to reconnect to Tunnel Service.|
|503|OTSTunnelServerUnavailable|The error message returned when an internal server error occurs.|Use exponential backoff to retry requests.|

