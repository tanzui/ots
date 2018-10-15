# Preface {#concept_vd4_2mj_bfb .concept}

Table Store is an Alibaba Cloud proprietary distributed NoSQL database. Nowadays, many application systems do not solely rely on relational databases. Instead, they select different database types depending on the business scenarios they are deployed in. For example, cached KeyValue data is stored in Redis, file type data is stored in MongoDB, and graph data is stored in Neo4J.

Traditional relational databases have difficulties in carrying data at such large scales, so a distributed database with high scalability is required. However, it is difficult to develop a highly available and highly scalable distributed database based on the traditional relational data model. If it is possible to use a model that is simpler than the relational data model and has simplified constraints and increased availability and scalability, business needs can be effectively met. It is based on these ideas that NoSQL was designed and developed.

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20269/153958674111631_en-US.png)

NoSQL has the following characteristics:

-   Multi-data models

    Many types of data models, such as KeyValue, Document, Wide Column, Graph, and Time Series, are provided to fulfill different data requirements. NoSQL databases operate far beyond the constraints of the relational data model and create a diversified business direction. These multi-data models are more suitable for all kinds of real-world scenarios.

-   High concurrency and low latency

    NoSQL is designed to provide high-concurrency and low-latency access to online businesses.

-   High scalability

    Scalability is one of the core design goals of NoSQL to cope with the explosive growth in volume of data.


DB-Engines aims at collecting information from DBMS \(Database Management System\) and development trend of multiple NoSQL databases from 2013 to 2018 is as follows:

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/20269/153958674111632_en-US.png)

It can be seen that all types of NoSQL databases have thrived in recent years. Alibaba Cloud Table Store, as a distributed NoSQL database, adopts a multi-model architecture and supports both Wide Column and Timeline architectures.

The Wide Column model, first put forward by Bigtable, has become the classic model among widely-used systems of the same type. Currently most semi-structured and structured data is stored in this model system. In addition to the Wide Column model, we have introduced a completely new data model called Timeline. The Timeline model is a new generation of model for messaging data. It is already widely used in instant messaging \(IM\), feeds, Internet of Things \(IoT\) equipment, and other systems where messaging data has to be stored and synchronized. These two models are described as follows:

