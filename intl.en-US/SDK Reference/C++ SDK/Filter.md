# Filter {#concept_67671_zh .concept}

Table Store can re-filter the read results on the service side to limit the amount of data transmitted on the network.

The internal node of the tree-structured filter is a logical operator \(CompositeColumnCondition\) and its leaf nodes are comparison operators \(SingleColumnCondition\).

-   CompositeColumnCondition supports AND, OR, and NOT. Two or more sub-nodes can be mounted on AND and OR. Only one node can be mounted on NOT.

-   SingleColumnCondition supports all 6 comparison conditions \(EQUAL TO, NOT EQUAL TO, GREATER THAN, LESS THAN, GREATER THAN OR EQUAL TO, LESS THAN OR EQUAL TO\).

-   Each SingleColumnCondition object supports comparing a column \(which can be a primary key column\) with a constant. Comparing two columns or two constants is not supported.

-   The latestVersionOnly parameter of SingleColumnCondition controls how multiple versions of the column value are involved in the comparison. The default value is “true”.

    -   If true, only the latest version of the column value, falling within the specified range of versions, is involved in the comparison. \(These column values are only involved in the comparison. If the filter accepts this row, other versions of the column value are still returned.\)

    -   If false, when any column value satisfies the conditions, the node considers it true.

-   The passIfMissing parameter of SingleColumnCondition controls what values the node is to consider if the column value is missing. The default value is “false”.


The filter acts on the results of other conditional filters. Therefore, if a column in the filter is not specified in the specified column read, or if no column value within the range of versions exists, the filter considers this column to be missing.

## Example {#section_imw_vsf_2fb .section}

The following example filters out from the table all rows that meet this condition: the primary key column pkey is greater than 1 and the attribute column attr is equal to “A”.

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

