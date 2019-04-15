# Timestream {#concept_183457 .concept}

Timestream模型是针对时序场景设计的模型，本文主要为您介绍Timestream模型及其应用。

## 抽象模型 {#section_0qr_gia_swy .section}

您可以通过Timestream的抽象模型对Timestream进行初步了解。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/157551/155531979744354_zh-CN.png)

|维度|描述|
|--|--|
|个体或群体（WHO）|描述产生数据的物体，可以是人、监控指标或者物体。通常描述个体会有多维的属性，可以通过某一类唯一ID来定位到个体，例如身份ID定位到人，设备ID定位到设备。也可以通过多维属性来定位到个体，例如通过集群、机器ID、进程名来定位到某个进程。|
|时间（WHEN）|时间是时序数据最重要的特征，是区别于其他数据的关键属性。|
|时空（WHERE）|通常是通过经纬度二维坐标定位到地点，在科学计算领域例如气象，通过经纬度和高度三维坐标来定位。|
|状态（WHAT）|用于描述特定个体在某一刻的状态，监控类时序数据通常是数值类型描述状态，轨迹数据是通过事件表述状态，不同场景有不同的表述方式。|

## 完整模型 {#section_utb_0w3_2mb .section}

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/157551/155531979844355_zh-CN.png)

时序数据包含元数据和数据点两个部分，其中，

-   元数据：由Name、Tags以及Attributes组成。Name+Tags可以唯一确定某个元数据。
-   数据点：由Timestamp、Location以及Fields组成。

|元素|描述|
|--|--|
|Name|定义数据的类别。|
|Tags|唯一标示个体的元数据。|
|Attributes|个体的可变属性。|
|Timestamp|数据产生的时间戳。|
|Location|数据产生的时空信息。|
|Fields|数据对应的值或状态，可提供多个值或状态，非必须是数值类型。|

## 应用案例 {#section_ew1_k1e_40l .section}

下面通过事件类的物流轨迹场景展示Timestream数据模型是如何使用的。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/157551/155531979844366_zh-CN.png)

以上是一个快递的物流轨迹数据，记录的是快递在不同时间点的状态变化。该轨迹数据的元数据则是快递本身，包含了单号、物流平台、快递当前位置信息以及快递寄件/收件等元信息，其中单号以及物流平台的组合（Identifier）唯一确定这个快递。下面分析一下数据存储方式：

-   将物流平台作为Name进行存储，一个快递平台的数据属于同一类数据，对数据检索性能有一定的提升。
-   将快递单号作为Tags存储，唯一确定一个快递。
-   快递的其他元信息存储在Attributes中，避免Tags过长导致的性能问题，同时能够支持这些信息的修改。
-   将快递的当前位置也存在在Attributes中，可以实现根据某个位置检索当前时间附近的快递。
-   快递的轨迹时序数据（位置/状态）放在Fields中，可以查询某个快递在某个时间范围内的轨迹。

