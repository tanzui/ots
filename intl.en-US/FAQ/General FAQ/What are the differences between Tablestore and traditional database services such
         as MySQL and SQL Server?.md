# What are the differences between Tablestore and traditional database services such as MySQL and SQL Server?

Tablestore is a multi-model NoSQL database service that is developed by Alibaba Cloud. It can store a large amount of structured data and supports fast query and analysis. Tablestore and traditional database services \(RDBMS such as MySQL and SQL Server\) differ in data models and technologies that are implemented.

Tablestore and traditional database services have the following differences:

-   You need to deploy and maintain the software and hardware of traditional database services. You are charged only for the actual resources you use such as storage resources and read/write capacity units \(CUs\) when you use Tablestore.
-   Traditional database services provide a variety of features such as such as view, indexing, and transaction. Tablestore provides features such as global secondary index, search index, and local transaction, which is superior in scalability to support large amounts of data \(several hundred TB\) and higher concurrency level of access \(100,000 QPS per table\).
-   Traditional database services use SQL statements. Tablestore provides standard HTTP RESTful APIs and SDKs in multiple languages.
-   Traditional database services require strict schemas. Tablestore does not use the tabular schema of rows and columns that are found in most traditional database systems. Instead, Tablestore uses a storage model that is optimized for the specific requirements of the type of data being stored. Columns in each row can be different. You can add or remove attribute columns.

