# Filter {#concept_43029_zh .concept}

Table Store filters are used to filter the results on the server side, so that the server only returns the rows or columns matching the filter conditions. Filters can be used with the GetRow, BatchGetRow, and GetRange APIs.

Currently, Table Store supports two filters: SingleColumnValueFilter and CompositeColumnValueFilter, which are used to determine whether to filter the data of a row based on reference column values. SingleColumnValueFilter only checks the value of a single reference column, whereas CompositeColumnValueFilter checks the values of multiple reference columns and forms a logical combination of the check results to determine whether to filter row data.

Note that the filters are used to filter the data that has been read; therefore, the reference column\(s\) used by SingleColumnValueFilter or CompositeColumnValueFilter must be included in the read data. If you specify the columns from which data is read, but these columns do not include any reference columns, the filters cannot obtain reference column values. When a reference column does not exist, SingleColumnValueFilter uses the passIfMissing parameter to determine whether the filter conditions are met. This means you can select an action even when no reference column exists.

**Example 1**

Construct a SingleColumnValueFilter.

```language-java
        // Set the filter; when the Col0 value is 0, this row is returned.
        SingleColumnValueFilter singleColumnValueFilter = new SingleColumnValueFilter("Col0",
                SingleColumnValueFilter.CompareOperator.EQUAL, ColumnValue.fromLong(0));
        // If the column Col0 does not exist, the data is not returned.
        singleColumnValueFilter.setPassIfMissing(false);
        // Only judge the latest version.
        singleColumnValueFilter.setLatestVersionsOnly(true);

```

**Example 2**

Construct a CompositeColumnValueFilter.

```language-java
        // The condition composite1 is: (Col0 == 0) AND (Col1 > 100).
        CompositeColumnValueFilter composite1 = new CompositeColumnValueFilter(CompositeColumnValueFilter.LogicOperator.AND);
        SingleColumnValueFilter single1 = new SingleColumnValueFilter("Col0",
                SingleColumnValueFilter.CompareOperator.EQUAL, ColumnValue.fromLong(0));
        SingleColumnValueFilter single2 = new SingleColumnValueFilter("Col1",
                SingleColumnValueFilter.CompareOperator.GREATER_THAN, ColumnValue.fromLong(100));
        composite1.addFilter(single1);
        composite1.addFilter(single2);

        // The condition composite2 is: ( (Col0 == 0) AND (Col1 > 100) ) OR (Col2 <= 10).
        CompositeColumnValueFilter composite2 = new CompositeColumnValueFilter(CompositeColumnValueFilter.LogicOperator.OR);
        SingleColumnValueFilter single3 = new SingleColumnValueFilter("Col2",
                SingleColumnValueFilter.CompareOperator.LESS_EQUAL, ColumnValue.fromLong(10));
        composite2.addFilter(composite1);
        composite2.addFilter(single3);

```

