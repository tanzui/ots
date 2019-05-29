# BoolQuery {#concept_ww3_rsc_fgb .concept}

You can call this operation to query data based on multiple conditions. BoolQuery may include one or more subquery conditions. You can retrieve the rows that match the subquery conditions.

You can combine these subquery conditions in different ways. If you specify these subquery conditions as MustQueries, Table Store returns results that meet all of the subquery conditions. If you specify these subquery conditions as MustNotQueries, Table Store returns results that meet none of the subquery conditions. BoolQuery has the following parameters:

BoolQuery has the following parameters:

```
/// <summary>
/// The document must match all subquery conditions.
/// </summary>
public List<IQuery> MustQueries { get; set; }
/// <summary>
/// The document must match none of the subquery conditions.
/// </summary>
public List<IQuery> MustNotQueries { get; set; }
/// <summary>
/// The document must match all subfilters. Unlike MustQueries, the matching score is ignored for FilterQueries.
/// </summary>
public List<IQuery> FilterQueries { get; set; }
/// <summary>
/// The document must match at least one of the subquery conditions. The score is higher if the document matches more subquery conditions.
/// </summary>
public List<IQuery> ShouldQueries { get; set; }
/// <summary>
/// Specify the minimum number of ShouldQueries clauses that the document must match. The default value is 1.
/// </summary>
public int? MinimumShouldMatch { get; set; }
```

