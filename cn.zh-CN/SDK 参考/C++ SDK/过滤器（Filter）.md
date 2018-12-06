# 过滤器（Filter） {#concept_67671_zh .concept}

表格存储可以在服务端对读取的结果再进行一次过滤，以便减少网络上传输的数据量。

过滤器是一个树形结构，内节点为逻辑运算（CompositeColumnCondition），叶节点为比较判断（SingleColumnCondition）。

-   CompositeColumnCondition支持与、或、非。其中与和或可以挂载两个或更多子节点，非只能挂载一个子节点。

-   SingleColumnCondition支持全部6种比较条件（等于、不等于、大于、小于、大于等于、小于等于）。

-   每个SingleColumnCondition对象支持一列（可以是主键列）和一个常量比较。不支持两列相比较，也不支持两个常量相比较。

-   SingleColumnCondition的latestVersionOnly参数控制多个版本的列值如何参与比较，默认为true。

    -   若为true，则只有版本范围内的最新版本列值参与比较（仅仅是参与比较，如果是过滤器认可该行，其他版本的列值依旧会返回）。

    -   若为false，则任意一个列值满足条件，该节点即认为true。

-   SingleColumnCondition的passIfMissing参数控制列值缺失时该节点应当视作何值，默认false。


过滤器作用于其他条件筛选的结果上。所以，如果过滤器中有某一列，而指定列读取中未指定该列，或版本范围内该列没有列值，则过滤器都认为该列缺失。

## 示例 {#section_imw_vsf_2fb .section}

以下示例从全表中过滤出主键列pkey大于1并且属性列attr等于A的行。

```language-cpp
RangeQueryCriterion query;
query.mutableTable() = kTableName;
query.mutableMaxVersions().reset(1);
{
  PrimaryKey& start = query.mutableInclusiveStart();
  start.append() = PrimaryKeyColumn(
      "pkey",
      PrimaryKeyValue::toInfMin());
}
{
  PrimaryKey& end = query.mutableExclusiveEnd();
  end.append() = PrimaryKeyColumn(
      "pkey",
      PrimaryKeyValue::toInfMax());
}
{
  // set filter
  shared_ptr<ColumnCondition> pkeyCond(
      new SingleColumnCondition(
          "pkey",
          SingleColumnCondition::kLarger,
          AttributeValue::toInteger(1)));
  shared_ptr<ColumnCondition> attrCond(
      new SingleColumnCondition(
          "attr",
          SingleColumnCondition::kEqual,
          AttributeValue::toStr("A")));
  shared_ptr<CompositeColumnCondition> top(new CompositeColumnCondition());
  top->mutableOp() = CompositeColumnCondition::kAnd;
  top->mutableChildren().append() = pkeyCond;
  top->mutableChildren().append() = attrCond;
  query.mutableFilter() = top;
}

```

