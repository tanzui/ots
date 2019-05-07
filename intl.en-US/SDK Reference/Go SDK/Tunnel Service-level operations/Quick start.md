# Quick start {#concept_sjd_g2j_kgb .concept}

## Initialize the Tunnel Service client {#section_hss_ffj_kgb .section}

```
// endpoint specifies the URL that is used to access a Table Store instance, such as https://instance.cn-hangzhou.ots.aliyun.com.
// instance specifies the name of an instance.
// AccessKeyId specifies the AccessKey ID that is used to access Table Store. AccessKeySecret specifies the corresponding AccessKey Secret that is used to access Table Store.
tunnelClient := tunnel.NewTunnelClient(endpoint, instance,
   accessKeyId, accessKeySecret)

```

## Create tunnels {#section_ssd_kfj_kgb .section}

```
req := &tunnel.CreateTunnelRequest{
   TableName:  "testTable",
   TunnelName: "testTunnel",
   Type:       tunnel.TunnelTypeBaseStream, //Set the type of the tunnel to differential.
}
resp, err := tunnelClient.CreateTunnel(req)
if err ! = nil {
   log.Fatal("create test tunnel failed", err)
}
log.Println("tunnel id is", resp.TunnelId)
```

## Obtain information of existing tunnels {#section_q4b_lfj_kgb .section}

```
req := &tunnel.DescribeTunnelRequest{
   TableName:  "testTable",
   TunnelName: "testTunnel",
}
resp, err := tunnelClient.DescribeTunnel(req)
if err ! = nil {
   log.Fatal("create test tunnel failed", err)
}
log.Println("tunnel id is", resp.Tunnel.TunnelId)
```

## Configure a callback function and start to consume data {#section_tzc_lfj_kgb .section}

```
// Define a callback function.
func exampleConsumeFunction(ctx *tunnel.ChannelContext, records []*tunnel.Record) error {
    fmt.Println("user-defined information", ctx.CustomValue)
    for _, rec := range records {
        fmt.Println("tunnel record detail:", rec.String())
    }
    fmt.Println("a round of records consumption finished")
    return nil
}

// Configure the callback function. Information about the callback function is passed to the SimpleProcessFactory API. Configure the TunnelWorkerConfig API.
workConfig := &tunnel.TunnelWorkerConfig{
   ProcessorFactory: &tunnel.SimpleProcessFactory{
      CustomValue: "user custom interface{} value",
      ProcessFunc: exampleConsumeFunction,
   },
}

// Specify TunnelDaemon to continuously consume the specified tunnel.
daemon := tunnel.NewTunnelDaemon(tunnelClient, tunnelId, workConfig)
log.Fatal(daemon.Run())
```

## Delete tunnels {#section_bvk_lfj_kgb .section}

```
req := &tunnel.DeleteTunnelRequest {
   TableName: "testTable",
   TunnelName: "testTunnel",
}
_, err := tunnelClient.DeleteTunnel(req)
if err ! = nil {
   log.Fatal("delete test tunnel failed", err)
}
```

