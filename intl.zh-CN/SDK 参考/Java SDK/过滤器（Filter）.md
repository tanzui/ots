# 过滤器（Filter） {#concept_43029_zh .concept}

过滤器 Filter 可以在服务器端对读取的结果再进行一次过滤，根据 Filter 中的条件决定返回哪些行或者列。Filter 可以用于 GetRow、BatchGetRow 和 GetRange 接口中。

目前表格存储仅支持 SingleColumnValueFilter 和 CompositeColumnValueFilter，这两个 Filter 都是基于参考列的列值决定某行是否会被过滤掉。前者只判断某个参考列的列值，后者会对多个参考列的列值判断结果进行逻辑组合，决定最终是否过滤。

需要注意的是，Filter 是对读取后的结果再进行一次过滤，所以 SingleColumnValueFilter 或者 CompositeColumnValueFilter 中的参考列必须在读取的结果内。如果用户指定了要读取的列，且其中不包含参考列，那么 Filter 无法获得这些参考列的值。当某个参考列不存在时，SingleColumnValueFilter 的 passIfMissing 参数决定此时是否满足条件，即用户可以选择当参考列不存在时的行为。

**示例1**

构造 SingleColumnValueFilter。

```language-java
        // 设置过滤器, 当Col0的值为0时返回该行.
        SingleColumnValueFilter singleColumnValueFilter = new SingleColumnValueFilter("Col0",
                SingleColumnValueFilter.CompareOperator.EQUAL, ColumnValue.fromLong(0));
        // 如果不存在Col0这一列, 也不返回.
        singleColumnValueFilter.setPassIfMissing(false);
        // 只判断最新版本
        singleColumnValueFilter.setLatestVersionsOnly(true);

```

**示例2**

构造 CompositeColumnValueFilter。

```language-java
        // composite1 条件为 (Col0 == 0) AND (Col1 > 100)
        CompositeColumnValueFilter composite1 = new CompositeColumnValueFilter(CompositeColumnValueFilter.LogicOperator.AND);
        SingleColumnValueFilter single1 = new SingleColumnValueFilter("Col0",
                SingleColumnValueFilter.CompareOperator.EQUAL, ColumnValue.fromLong(0));
        SingleColumnValueFilter single2 = new SingleColumnValueFilter("Col1",
                SingleColumnValueFilter.CompareOperator.GREATER_THAN, ColumnValue.fromLong(100));
        composite1.addFilter(single1);
        composite1.addFilter(single2);

        // composite2 条件为 ( (Col0 == 0) AND (Col1 > 100) ) OR (Col2 <= 10)
        CompositeColumnValueFilter composite2 = new CompositeColumnValueFilter(CompositeColumnValueFilter.LogicOperator.OR);
        SingleColumnValueFilter single3 = new SingleColumnValueFilter("Col2",
                SingleColumnValueFilter.CompareOperator.LESS_EQUAL, ColumnValue.fromLong(10));
        composite2.addFilter(composite1);
        composite2.addFilter(single3);

```

