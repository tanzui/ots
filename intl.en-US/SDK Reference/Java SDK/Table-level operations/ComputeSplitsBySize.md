# ComputeSplitsBySize {#concept_gxk_k3h_1gb .concept}

You can call this operation to specify the concurrency level of the compute engine. The ComputeSplitsBySize operation logically divides the data in a specified table into several splits approximately in the size specified, and returns the split points among these splits and the instances where the splits are located.

## Example {#section_wtk_y3h_1gb .section}

```
private static void describeTable(SyncClient client) {
    // Split data by 200 MB.
    ComputeSplitsBySizeRequest request = new ComputeSplitsBySizeRequest(TABLE_NAME, 2);
    ComputeSplitsBySizeResponse response = client.computeSplitsBySize(computeSplitsBySizeRequest);
    System.out.println("ConsumedCapacity=" + response.getConsumedCapacity().jsonize());
    System.out.println("PrimaryKeySchema=" + response.getPrimaryKeySchema());
    System.out.println("RequestId=" + response.getRequestId());
    System.out.println("TraceId=" + response.getTraceId());
    List<Split> splits = response.getSplits();
    System.out.println("splits.size=" + splits.size());
    Iterator<Split> iterator = splits.iterator();
    while (iterator.hasNext()) {
        Split split = iterator.next();
        System.out.println("split.getLocation()=" + split.getLocation());
        // You can inject split.getLowerBound() and split.getUpperBound() into RangeRowQueryCriteria, and pass RangeRowQueryCriteria to getRange() or createRangeIterator().
        System.out.println("split.getLowerBound()=" + split.getLowerBound().jsonize());
        System.out.println("split.getLowerBound()=" + split.getUpperBound().jsonize());
    }
}
```

