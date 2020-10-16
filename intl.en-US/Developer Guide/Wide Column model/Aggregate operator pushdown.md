# Aggregate operator pushdown

This topic describes the aggregate operator pushdown feature and the scenarios where this feature is used.

## Background information

Data statistics are a big concern during the data scanning process. For example, the COUNT or SUM operation is often performed on the column values within a data range. Without aggregation functions, you must read data from a table to the client and then perform statistical operations on the data. This poses two problems:

-   Data ingestion incurs a large bandwidth overhead.
-   Data is aggregated on the client, which results in low computing concurrency and limited computing capability.

Tablestore supports pushing down the COUNT, SUM, MAX, MIN, and GROUP BY aggregate operators and aggregating data on the backend server. The server returns the aggregate results to clients. Aggregate operator pushdown maximizes computing concurrency and avoids additional bandwidth consumption that is incurred by returning raw data to the client.

## API operations

The following types of operators can be pushed down:

-   COUNT, SUM, MAX, and MIN aggregate operators
-   The GROUP BY function

When you call API operations, you can pass in multiple aggregate operators and provide a aggregation function that includes the GROUP BY operator.

```
message ScanOperation {
    repeated BuiltInAggFunctionPB aggregation_function = 1;
    optional BuiltInGroupByFunctionPB groupby_function = 2;
}
```

Tablestore provides statistical operations to calculate the number of rows, number of columns, sum of column values, and maximum and minimum column values.

**Note:** You do not need to specify a column name when you count the number of rows. However, you must specify a column name for statistical operations based on column values.

-   Definition of an aggregate function

    ```
    enum BuiltInAggFunctionType {
        COUNT_ROW_FUNC = 1;
        COUNT_COLUMN_FUNC = 2;
        MIN_FUNC = 3;
        MAX_FUNC = 4;
        SUM_FUNC = 5;
    }
    
    message BuiltInAggFunctionPB {
        required BuiltInAggFunctionType type = 1;
        optional bytes column_name = 2;
    }
    ```

-   Definition of a GROUP BY function

    ```
    enum BuiltInGroupByFunctionType {
        GROUP_BY_COLUMN_NAME = 1;
    }
    
    message BuiltInGroupByColumnNamePB {
        repeated bytes column_name = 1;
    }
    
    message BuiltInGroupByFunctionPB {
        required BuiltInGroupByFunctionType type = 1;
        optional bytes function = 2;
    }
    ```

    When you call a GROUP BY function on columns, you can specify a single- or multi-column grouping key.

-   Return values
    -   Format of return values

        If you use an aggregation function in the request, the scan operation does not return raw data. One of the following calculation results is returned in the PlainBuffer format:

        -   A result row is returned if the aggregation function does not contain the GROUP BY clause. Schema: The primary key is empty, and the attribute column values are the calculation results of each AggFn in the same order as the requested AggFn parameters.
        -   Multiple result rows are returned if the aggregation function contains the GROUP BY clause. Schema: The primary key is group-by key, and the attribute column values are the calculation results of each AggFn in the same order as the requested AggFn parameters.
        **Note:**

        -   If the scan range is large, use the scan token to return results in a multi-part format.
        -   The timestamp field in the return value column is empty.
    -   Naming conventions of returned column names
        -   Count the number of rows: `COUNT_ROW_FUNC#`
        -   Sum up column values: `SUM_FUNC#Column name`
        -   Count the number of columns: `COUNT_COLUMN_FUNC#Column name`
        -   Calculate the minimum column value: `MIN_FUNC#Column name`
        -   Calculate the maximum column value: `MAX_FUNC#Column name`

## Scenarios

You use a table named table-1 to store the CPU usage data of different servers at different time points. The schema of table-1 is in the following format:

```
Primary key column 1 = host_name(string)| Primary key column 2 = time(int64_t) | Attribute column = cpu_usage(int64_t)
```

The primary key consists of host\_name and time. The data is sorted based on the time values. The CPU usage of a server at each time point is stored in the attribute column of each row.

-   Scenario 1: Calculate the maximum CPU usage of host-1 in a specific period
    -   Equivalent SQL example

        ```
        select max(cpu_usage) from 'cpu_table' where time >= 100 and time < 200 and host_name = 'host-1';
        ```

    -   Sample code

        ```
            PrimaryKey inclusiveStartPK;
            PrimaryKey exclusiveEndPK;
            inclusiveStartPK.AddPrimaryKeyColumn("host_name", PrimaryKeyValue("host-1"));
            inclusiveStartPK.AddPrimaryKeyColumn("time", PrimaryKeyValue(100));
            exclusiveEndPK.AddPrimaryKeyColumn("host_name", PrimaryKeyValue("host-1"));
            exclusiveEndPK.AddPrimaryKeyColumn("time", PrimaryKeyValue(200));
        
            // Set the MAX function.
            ScanOperation scanOperation;
            list<AggFunction> aggFunctions;
            {
                AggFunction aggFunction(MAX_FUNC, "cpu_usage");
                aggFunctions.push_back(aggFunction);
            }
            scanOperation.SetAggFunctions(aggFunctions);
        
            // Initiate a request and obtain the results.
            while (inclusiveStartPK.GetPrimaryKeyColumnsSize() > 0) {
                RangeRowQueryCriteria queryCriteria("cpu_table");
                queryCriteria.SetInclusiveStartPrimaryKey(inclusiveStartPK);
                queryCriteria.SetExclusiveEndPrimaryKey(exclusiveEndPK);
                queryCriteria.SetDirection(RangeRowQueryCriteria::FORWARD);
                queryCriteria.SetMaxVersions(1);
                queryCriteria.SetScanOperation(scanOperation);
        
                GetRangeRequestPtr getRangeRequestPtr(new GetRangeRequest(queryCriteria));
                GetRangeResultPtr getRangeResultPtr = gClient->GetRange(getRangeRequestPtr);
                const list<RowPtr>& rowPtrs = getRangeResultPtr->GetRows();
                typeof(rowPtrs.begin()) iter = rowPtrs.begin();
                for (; iter ! = rowPtrs.end(); ++iter) {// Aggregate the results on the client.
                    const Column& col = ((*iter)->GetColumns()).front();
                    colName = col.GetName();
                    int64_t v = col.GetValue().AsInteger();
                    maxCpuUsage = maxCpuUsage < v ? v: maxCpuUsage;
                }
        
                if (getRangeResultPtr->HasNextStartPrimaryKey()) {
                    inclusiveStartPK = getRangeResultPtr->GetNextStartPrimaryKey();
                } else {
                    inclusiveStartPK = PrimaryKey();
                }
            }
            std::cout << "AggName: " << colName << ", AggValue: " << maxCpuUsage << std::endl;
        							
        ```

    -   Response

        ```
        AggName: MAX_FUNC#cpu_usage, AggValue: 75
        ```

-   Scenario 2: Calculate the minimum CPU usage of host-1 in a specific period
    -   Equivalent SQL example

        ```
        select min(cpu_usage) from 'cpu_table' where time >= 100 and time < 200 and host_name = 'host-1';
        ```

    -   Sample code

        ```
            PrimaryKey inclusiveStartPK;
            PrimaryKey exclusiveEndPK;
            inclusiveStartPK.AddPrimaryKeyColumn("host_name", PrimaryKeyValue("host-1"));
            inclusiveStartPK.AddPrimaryKeyColumn("time", PrimaryKeyValue(100));
            exclusiveEndPK.AddPrimaryKeyColumn("host_name", PrimaryKeyValue("host-1"));
            exclusiveEndPK.AddPrimaryKeyColumn("time", PrimaryKeyValue(200));
        
            // Set the MIN function.
            ScanOperation scanOperation;
            list<AggFunction> aggFunctions;
            {
                AggFunction aggFunction(MIN_FUNC, "cpu_usage");
                aggFunctions.push_back(aggFunction);
            }
            scanOperation.SetAggFunctions(aggFunctions);
        
            // Initiate a request and obtain the results.
            int64_t minCpuUsage = INT64_MAX;
            std::string colName;
            while (inclusiveStartPK.GetPrimaryKeyColumnsSize() > 0) {
                RangeRowQueryCriteria queryCriteria("cpu_table");
                queryCriteria.SetInclusiveStartPrimaryKey(inclusiveStartPK);
                queryCriteria.SetExclusiveEndPrimaryKey(exclusiveEndPK);
                queryCriteria.SetDirection(RangeRowQueryCriteria::FORWARD);
                queryCriteria.SetMaxVersions(1);
                queryCriteria.SetScanOperation(scanOperation);
        
                GetRangeRequestPtr getRangeRequestPtr(new GetRangeRequest(queryCriteria));
                GetRangeResultPtr getRangeResultPtr = gClient->GetRange(getRangeRequestPtr);
                const list<RowPtr>& rowPtrs = getRangeResultPtr->GetRows();
                typeof(rowPtrs.begin()) iter = rowPtrs.begin();
                for (; iter ! = rowPtrs.end(); ++iter) {// Aggregate the results on the client.
                    const Column& col = ((*iter)->GetColumns()).front();
                    colName = col.GetName();
                    int64_t v = col.GetValue().AsInteger();
                    minCpuUsage = minCpuUsage > v ? v: minCpuUsage;
                }
        
                if (getRangeResultPtr->HasNextStartPrimaryKey()) {
                    inclusiveStartPK = getRangeResultPtr->GetNextStartPrimaryKey();
                } else {
                    inclusiveStartPK = PrimaryKey();
                }
            }
            std::cout << "AggName: " << colName << ", AggValue: " << minCpuUsage << std::endl;
        ```

    -   Response

        ```
        AggName: MIN_FUNC#cpu_usage, AggValue: 9
        ```

-   Scenario 3: Count the number of data records of host-1 in a specific period
    -   Equivalent SQL example

        ```
        select count(*) from 'cpu_table' where time >= 100 and time < 200 and host_name = 'host-1';
        ```

    -   Sample code

        ```
            PrimaryKey inclusiveStartPK;
            PrimaryKey exclusiveEndPK;
            inclusiveStartPK.AddPrimaryKeyColumn("host_name", PrimaryKeyValue("host-1"));
            inclusiveStartPK.AddPrimaryKeyColumn("time", PrimaryKeyValue(100));
            exclusiveEndPK.AddPrimaryKeyColumn("host_name", PrimaryKeyValue("host-1"));
            exclusiveEndPK.AddPrimaryKeyColumn("time", PrimaryKeyValue(200));
        
            // Set the COUNT function.
            ScanOperation scanOperation;
            list<AggFunction> aggFunctions;
            {
                AggFunction aggFunction(COUNT_ROW_FUNC);
                aggFunctions.push_back(aggFunction);
            }
            scanOperation.SetAggFunctions(aggFunctions);
        
            // Initiate a request and obtain the results.
            int64_t cpuRowCount = 0;
            std::string colName;
            while (inclusiveStartPK.GetPrimaryKeyColumnsSize() > 0) {
                RangeRowQueryCriteria queryCriteria("cpu_table");
                queryCriteria.SetInclusiveStartPrimaryKey(inclusiveStartPK);
                queryCriteria.SetExclusiveEndPrimaryKey(exclusiveEndPK);
                queryCriteria.SetDirection(RangeRowQueryCriteria::FORWARD);
                queryCriteria.SetMaxVersions(1);
                queryCriteria.SetScanOperation(scanOperation);
        
                GetRangeRequestPtr getRangeRequestPtr(new GetRangeRequest(queryCriteria));
                GetRangeResultPtr getRangeResultPtr = gClient->GetRange(getRangeRequestPtr);
                const list<RowPtr>& rowPtrs = getRangeResultPtr->GetRows();
                typeof(rowPtrs.begin()) iter = rowPtrs.begin();
                for (; iter ! = rowPtrs.end(); ++iter) {// Aggregate the results on the client.
                    const Column& col = ((*iter)->GetColumns()).front();
                    colName = col.GetName();
                    cpuRowCount += col.GetValue().AsInteger();
                }
        
                if (getRangeResultPtr->HasNextStartPrimaryKey()) {
                    inclusiveStartPK = getRangeResultPtr->GetNextStartPrimaryKey();
                } else {
                    inclusiveStartPK = PrimaryKey();
                }
            }
            std::cout << "AggName: " << colName << ", AggValue: " << cpuRowCount << std::endl;
        ```

    -   Response

        ```
        AggName: COUNT_ROW_FUNC#, AggValue: 7
        ```

-   Scenario 4: Calculate the average CPU usage of each server in a specific period
    -   Equivalent SQL example

        ```
        select host_name, avg(cpu_usage) from cpu_table 
            where time >= 100 and time < 200 and 
                host_name >= 'host-100' and host_name < 'host-200'
            group by host_name
        ```

    -   Sample code

        In this scenario, the statistical results are returned in batches because a large amount of data is returned. Therefore, to calculate the average CPU usage, you must accumulate the results of multiple rounds of `sum(cpu_usage)` and `count(cpu_usage)`. The calculation is divided into two steps:

        1.  Sum up the CPU usage of each server in a specific period and the number of CPU records.

            ```
                PrimaryKey inclusiveStartPK;
                PrimaryKey exclusiveEndPK;
                inclusiveStartPK.AddPrimaryKeyColumn("host_name", PrimaryKeyValue("host-100"));
                inclusiveStartPK.AddPrimaryKeyColumn("time", PrimaryKeyValue(100));
                exclusiveEndPK.AddPrimaryKeyColumn("host_name", PrimaryKeyValue("host-200"));
                exclusiveEndPK.AddPrimaryKeyColumn("time", PrimaryKeyValue(200));
            
                // Set the SUM and COUNT functions.
                ScanOperation scanOperation;
                list<AggFunction> aggFunctions;
                {
                    AggFunction aggFunction(SUM_FUNC, "cpu_usage");
                    aggFunctions.push_back(aggFunction);
                }
                {
                    AggFunction aggFunction(COUNT_COL_FUNC, "cpu_usage");
                    aggFunctions.push_back(aggFunction);
                }
                scanOperation.SetAggFunctions(aggFunctions);
            
                // Set a GROUP BY function based on host_name.
                {
                    list<string> groupByKeys;
                    groupByKeys.push_back("host_name");
                    GroupByFunction groupByFunction(GROUP_BY_COLUMN_NAME, groupByKeys);
                    scanOperation.SetGroupByFunction(groupByFunction);
                }
            
                // Initiate a request and obtain the results.
                std::map<
                    std::string /* host name*/,
                    std::pair<int64_t /* cpu sum */, int64_t /*cpu count*/>
                > result;
                while (inclusiveStartPK.GetPrimaryKeyColumnsSize() > 0) {
                    RangeRowQueryCriteria queryCriteria("cpu_table");
                    queryCriteria.SetInclusiveStartPrimaryKey(inclusiveStartPK);
                    queryCriteria.SetExclusiveEndPrimaryKey(exclusiveEndPK);
                    queryCriteria.SetDirection(RangeRowQueryCriteria::FORWARD);
                    queryCriteria.SetMaxVersions(1);
                    queryCriteria.SetScanOperation(scanOperation);
            
                    GetRangeRequestPtr getRangeRequestPtr(new GetRangeRequest(queryCriteria));
                    GetRangeResultPtr getRangeResultPtr = gClient->GetRange(getRangeRequestPtr);
                    const list<RowPtr>& rowPtrs = getRangeResultPtr->GetRows();
                    typeof(rowPtrs.begin()) iter = rowPtrs.begin();
                    for (; iter ! = rowPtrs.end(); ++iter) {// Aggregate the results on the client.
                        const std::string& hostName = ((*iter)->GetPrimaryKey()).GetColumn(0).GetValue().AsString();
                        const Column& sumCol = ((*iter)->GetColumns()).front();
                        const Column& countCol = ((*iter)->GetColumns()).back();
                        int64_t cpuSum = sumCol.GetValue().AsInteger();
                        int64_t cpuCount = countCol.GetValue().AsInteger();
                        if (result.find(hostName) ! = result.end()) {
                            result[hostName].first +=cpuSum;
                            result[hostName].second += cpuCount;
                        } else {
                            result[hostName] = std::make_pair<int64_t, int64_t>(cpuSum, cpuCount);
                        }
                    }
            
                    // Obtain multiple rounds of results.
                    if (getRangeResultPtr->HasNextStartPrimaryKey()) {
                        inclusiveStartPK = getRangeResultPtr->GetNextStartPrimaryKey();
                    } else {
                        inclusiveStartPK = PrimaryKey();
                    }
                }
            ```

        2.  Calculate the average CPU usage of each server such as host1 on the client:`avg(host1) = sum(host1-cpu) /count (host1-cpu)`.

            ```
                typeof(result.begin()) rIt = result.begin();
                for (; rIt ! = result.end(); ++rIt) {
                    double cpuAverage = double(rIt->second.first) / rIt->second.second;
                    std::cout << "HostName: " << rIt->first << ", CpuAverage: " << cpuAverage << std::endl;
                }
            ```

    -   Response

        ```
        HostName: host-100, CpuAverage: 38.7667
        HostName: host-101, CpuAverage: 50.4333
        HostName: host-102, CpuAverage: 48.0667
        HostName: host-103, CpuAverage: 46.5333
        HostName: host-104, CpuAverage: 43.8333
        HostName: host-105, CpuAverage: 40.5333
        HostName: host-106, CpuAverage: 48.2667
        HostName: host-107, CpuAverage: 54.5667
        HostName: host-108, CpuAverage: 42.9667
        HostName: host-109, CpuAverage: 47.5667
        ```


## Limits

-   Aggregate functions
    -   Do not specify a column name when you use COUNT\_ROW\_FUNC. Otherwise, an exception is returned.
    -   A column name must be specified when you use COUNT\_COLUMN, MIN, MAX, and SUM\_FUNC. Otherwise, an exception is returned.
    -   The return value of a COUNT operation on rows or columns is of the INTEGER type.
    -   The MIN, MAX, and SUM operations support only the INTEGER and DOUBLE types.
    -   An exception is returned when the calculation result \(INT64 or DOUBLE\) of a COUNT or SUM operation overflows.
    -   The initial value of a COUNT operation on rows or columns is 0. The initial value of a MIN, MAX, or SUM operation is NULL. When the column you want to read does not exist, the initial value is returned.
    -   When a COUNT\_COLUMN, MIN, MAX, or SUM operation is performed, an exception is returned if column values are of different types.
    -   The aggregation feature is billed in the same way as the Scan operation.
-   GROUP BY function
    -   Group column values must be of the INTEGER, STRING, BLOB, and BOOLEAN types. Otherwise, an exception is returned.
    -   An exception is returned when group column values are of different types.

