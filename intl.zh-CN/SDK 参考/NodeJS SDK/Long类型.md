# Long类型 {#concept_56356_zh .concept}

表格存储提供了[五种数据类型](../../../../intl.zh-CN/数据模型/Wide Column/基本概念/主键和属性.md#ul_yjn_zyf_cfb)，与NodeJS SDK的数据类型对应关系如下：

|数据类型|NodeJS SDK|描述|
|:---|:---------|:-|
|String|string|JavaScript语言中的基本数据类型|
|Integer|int64|NodeJs SDK封装的数据类型|
|Dobule|number|javascript语言中的基本数据类型|
|Boolean|boolean|javascript语言中的基本数据类型|
|Binary|Buffer|NodeJS的Buffer对象|

**int64类型**

表格存储的Integer类型是一个64位的有符号整型，这个数据类型在JavaScript中没有相应的数据类型可以对应，所以在NodeJS中也需要一个能表示64位有符号整型的数据类型。

可以做如下转换：

```language-javascript
var numberA = TableStore.Long.fromNumber(1000);
var numberB = TableStroe.Long.fromString('2000');

var num = numberA.toNumber();
    num = numberA.toString();

var str = numberB.toNumber();
    str = numberB.toString();

```

