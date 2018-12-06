# StreamRecord {#reference303 .reference}

StreamRecord indicates a data row in the response message of the GetStreamRecord operation.

## Data structure {#section_ysh_jyq_dfb .section}

```language-xml
 message StreamRecord {
        required ActionType action_type = 1;
        required bytes record = 2;
}

```

action\_type:

-   Type: Required [ActionType](reseller.en-US/API Reference/Data Types/ActionType.md#) 

-   The operation type of the row.


record:

-   Type: Required bytes

-   The data content of the row.


## Related operations { .section}

 [GetStreamRecord](reseller.en-US/API Reference/Operations/GetStreamRecord.md#) 

