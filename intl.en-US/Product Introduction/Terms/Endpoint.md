# Endpoint {#concept_bsx_btj_bfb .concept}

Each instance corresponds to an endpoint that is also known as the connection URL. The endpoint needs to be specified before any operations on the tables and data of Table Store.

-   To access the data in Table Store from the Internet, the endpoint uses the following format:

    ```
    https://instanceName.region.ots.aliyuncs.com
    ```

-   To access the data in Table Store from an Alibaba Cloud ECS instance of the same region through the intranet, the endpoint uses the following format:

    ```
    https://instanceName.region.ots-internal.aliyuncs.com
    ```

    For example, to access the Table Store instance in China East 1 \(Hangzhou\) region, with the instance name of myInstance:

    ```
    Endpoint for Internet access: https://myInstance.cn-hangzhou.ots.aliyuncs.com
      Endpoint for intranet access: https://myInstance.cn-hangzhou.ots-internal.aliyuncs.com
    ```

    Better performance, such as lower response latency and no unnecessary Internet traffic, can be expected through the intranet.

-   If an application accesses Table Store from an ECS instance in VPC, the endpoint uses the following format:

    ```
    https://vpcName-instanceName.region.vpc.ots.aliyuncs.com
    ```

    For example, the service address used by an application in China East 1 \(Hangzhou\) region to access the instance named myInstance from a network named testVPC:

    ```
    Endpoint of VPC access: https://testVPC-myInstance.cn-hangzhou.vpc.ots.aliyuncs.com
    ```

    This VPC access address is only used for access initiated by servers in the testVPC network.


