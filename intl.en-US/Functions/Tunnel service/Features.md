# Features {#concept_usp_ss3_kgb .concept}

Tunnel Service is an integrated service for full and incremental data consumption based on Table Store API. Tunnel Service provides the following features:

## Tunnels for full and incremental data consumption {#section_o4y_nps_ngb .section}

Tunnel Service allows you to consume incremental data, full data, and full and incremental data simultaneously.

## Orderly incremental data consumption {#section_v1q_lps_ngb .section}

Tunnel Service distributes incremental data to one or more logical partitions sequentially based on the write time. Data in different partitions can be consumed simultaneously.

## Consumption latency monitoring {#section_e4m_4ps_ngb .section}

Tunnel Service allows you to call the DescribeTunnel operation to view the recovery point objective \(RPO\) information of the consumed data on each client. Tunnel Service also allows you to monitor data consumption of tunnels in the Table Store console.

## Horizontal scaling of data consumption capabilities {#section_ocq_4ps_ngb .section}

Tunnel Service supports automatic load balancing among logical partitions. With this feature, you can add more Tunnel Clients to accelerate data consumption.

