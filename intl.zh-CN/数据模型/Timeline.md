# Timeline {#concept_89885_zh .concept}

Timeline 模型是针对消息数据场景所设计的数据模型，它能满足消息数据场景对消息保序、海量消息存储、实时同步的特殊需求。

![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/44276/cn_zh/1533028483643/4.PNG)

**说明：** 将一张大的数据表内的数据抽象为多个 Timeline，能够承载的 Timeline 个数无上限。

Timeline 的构成主要包括：

-   Timeline ID：用于唯一标识 Timeline。
-   Timeline Meta：Timeline 的元数据，元数据内可包含任意键值对属性。
-   Message Sequence：消息队列，承载该 Timeline下的所有消息。 \>**说明**： 消息在队列里有序保存，并且根据写入顺序分配自增的 ID。一个消息队列可承载的消息个数无上限，在消息队列内部可根据消息 ID 随机定位某条消息，并提供正序或者反序扫描。
-   Message Entry：消息体，包含消息的具体内容，可以包含任意键值对。

