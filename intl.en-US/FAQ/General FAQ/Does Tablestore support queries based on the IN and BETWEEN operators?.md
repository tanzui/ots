# Does Tablestore support queries based on the IN and BETWEEN operators?

Yes. You can implement queries similar to the IN operator-based queries based on the following conditions:

-   If you can specify complete primary key columns, call the BatchGetRow operation. For more information, see [BatchGetRow](/intl.en-US/SDK Reference/Java SDK/Multiple-row operations.md).
-   If you cannot specify the complete primary key columns or attribute columns, use terms query of search index. For more information, see [TermsQuery](/intl.en-US/Developer Guide/Search Index/API operations/TermsQuery.md).

You can implement queries similar to the BETWEEN operator-based queries based on the following conditions:

-   If you can specify complete primary key columns, call the GetRow operation. For more information, see [GetRange](/intl.en-US/SDK Reference/Java SDK/Multiple-row operations.md).
-   If you cannot specify the complete primary key columns or attribute columns, use range query of search index. For more information, see [Range query](/intl.en-US/Developer Guide/Search Index/API operations/Range query.md).

