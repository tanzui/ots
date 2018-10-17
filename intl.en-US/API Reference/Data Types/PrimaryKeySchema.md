# PrimaryKeySchema {#reference323 .reference}

PrimaryKeySchema specifies the attribute value of the primary key.

## Data structure {#section_wny_wsq_dfb .section}

```language-protobuf
message PrimaryKeySchema {
    required string name = 1;
    required PrimaryKeyType type = 2;
    optional PrimaryKeyOption option = 3; 
}

```

name:

-   Type: String

-   The name of the column.


type:

-   Type: [PrimaryKeyType](reseller.en-US/API Reference/Data Types/PrimaryKeyType.md#) 

-   The type of the column.


option:

-   Type: [PrimaryKeyOption](reseller.en-US/API Reference/Data Types/PrimaryKeyOption.md#) 

-   The additional attribute value of the column.


