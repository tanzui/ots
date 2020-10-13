# Features

Tunnel Service provides tunnels for full and incremental data consumption, orderly incremental data consumption, consumption latency monitoring, and horizontal scaling of data consumption capabilities.

**Note:** In scenarios where a table is written for 100,000 times per seconds, Tunnel Service provides a latency of milliseconds from the data is updated until the update record is obtained. The update record is returned in the sequence that the data is updated.

## Tunnels for full and incremental data consumption

Tunnel Service allows you to consume incremental data, full data, and full and incremental data simultaneously.

## Orderly incremental data consumption

Tunnel Service sequentially distributes incremental data to one or more logical partitions based on the write time. Data in different partitions can be consumed simultaneously.

## Consumption latency monitoring

Tunnel Service allows you to call the DescribeTunnel operation to view the latency \(time point\) of the consumed data on each client. Tunnel Service also allows you to monitor data consumption of tunnels in the Tablestore console.

## Horizontal scaling of data consumption capabilities

Tunnel Service supports automatic load balancing among logical partitions. With this feature, you can add more tunnel clients to accelerate data consumption.

