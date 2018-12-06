# Long type {#concept_56356_zh .concept}

Table Store provides five [data types](../../../../reseller.en-US/Data Models/Wide Column/Basic concepts/Primary key and attribute.md#ul_yjn_zyf_cfb). The relationship between the Table Store data types and the NodeJS SDK data types is as follows:

|Table Store|NodeJS SDK|Description|
|:----------|:---------|:----------|
|String|string|The basic data type in JavaScript|
|Interger|int64|The data type for NodeJs SDK encapsulation|
|Dobule|number|The basic data type in JavaScript|
|Boolean|boolean|The basic data type in JavaScript|
|Binary|Buffer|The buffer object of NodeJS|

**int64 type**

The Integer type of Table Store is a 64-bit signed integer, which does not have a corresponding data type in JavaScript. Therefore, a data type that can represent a 64-bit signed integer is required for NodeJS.

You can perform the following conversions.

```language-javascript
var numberA = TableStore.Long.fromNumber(1000);
var numberB = TableStroe.Long.fromString('2000');

var num = numberA.toNumber();
    num = numberA.toString();

var str = numberB.toNumber();
    str = numberB.toString();

```

