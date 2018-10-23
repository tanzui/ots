# PlainBuffer {#reference3003 .reference}

因为 Protocol Buffer 序列化和解析小对象的性能很差，所以表格存储自定义了 PlainBuffer 数据格式用来表示行数据。

## 格式定义 {#section_cvs_nsq_dfb .section}

```
plainbuffer = tag_header row1  [row2]  [row3]
row = ( pk [attr] | [pk] attr | pk attr ) [tag_delete_marker] row_checksum;
pk = tag_pk cell_1 [cell_2] [cell_3]
attr  = tag_attr cell1 [cell_2] [cell_3]
cell = tag_cell cell_name [cell_value] [cell_op] [cell_ts] cell_checksum
cell_name = tag_cell_name  formated_value
cell_value = tag_cell_value formated_value
cell_op = tag_cell_op  cell_op_value
cell_ts = tag_cell_ts cell_ts_value
row_checksum = tag_row_checksum row_crc8
cell_checksum = tag_cell_checksum row_crc8

formated_value = value_type value_len value_data
value_type = int8
value_len = int32

cell_op_value = delete_all_version | delete_one_version
cell_ts_value = int64 
delete_all_version = 0x01 (1byte)
delete_one_version = 0x03 (1byte)

```

## Tag取值 { .section}

```
tag_header = 0x75 (4byte)
tag_pk = 0x01 (1byte)
tag_attr = 0x02 (1byte)
tag_cell = 0x03 (1byte)
tag_cell_name = 0x04 (1byte)
tag_cell_value = 0x05 (1byte)
tag_cell_op = 0x06 (1byte)
tag_cell_ts = 0x07 (1byte)
tag_delete_marker = 0x08 (1byte)
tag_row_checksum = 0x09 (1byte)
tag_cell_checksum = 0x0A (1byte)

```

## ValueType 取值 { .section}

formated\_value 中 value\_type 的取值如下：

```
VT_INTEGER = 0x0
VT_DOUBLE = 0x1
VT_BOOLEAN = 0x2
VT_STRING = 0x3
VT_NULL = 0x6
VT_BLOB = 0x7
VT_INF_MIN = 0x9
VT_INF_MAX = 0xa
VT_AUTO_INCREMENT = 0xb

```

## 计算 Checksum { .section}

计算 checksum 的基本逻辑是：

-   针对每个 cell 的 name/value/type/timestamp 计算。
-   针对 row 里面的 delete 计算，其中有 delete mark 补单字节1；若没有，补单字节0。
-   因为对每个 cell 计算了 checksum，所以计算 row 的 checksum 的时候直接利用 cell 的checksum 来计算，即 row 的 crc 只对 cell 的 checksum 做 crc，不对数据做 crc。

Java 实现：

**说明：** 以下代码摘自`$tablestore-4.2.1-sources/com/alicloud/openservices/tablestore/core/protocol/PlainBufferCrc8.java`，具体参见[Java SDK](../../../../intl.zh-CN/SDK 参考/Java SDK/安装.md)。

```language-c++
public static byte getChecksum(byte crc, PlainBufferCell cell) throws IOException {

    if (cell.hasCellName()) {
        crc = crc8(crc, cell.getNameRawData());
    }

    if (cell.hasCellValue()) {
        if (cell.isPk()) {
            crc = cell.getPkCellValue().getChecksum(crc);
        } else {
            crc = cell.getCellValue().getChecksum(crc);
        }
    }

    if (cell.hasCellTimestamp()) {
        crc = crc8(crc, cell.getCellTimestamp());
    }

    if (cell.hasCellType()) {
        crc = crc8(crc, cell.getCellType());
    }

    return crc;
}

public static byte getChecksum(byte crc, PlainBufferRow row) throws IOException {
    for (PlainBufferCell cell : row.getPrimaryKey()) {
        crc = crc8(crc, cell.getChecksum());
    }

    for (PlainBufferCell cell : row.getCells()) {
        crc = crc8(crc, cell.getChecksum());
    }

    byte del = 0;
    if (row.hasDeleteMarker()) {
        del = (byte)0x1;
    }
    crc = crc8(crc, del);

    return crc;
}

```

## 举例 { .section}

假设一行数据有两列主键，4 列数据。其中主键类型为 string 和 integer，3 列数据中分别是 string、integer 和 double，版本分别是 1001、1002 和 1003，还有一列是删除所有版本。

-   主键列：
    -   \[pk1:string:iampk\]
    -   \[pk2:integer:100\]
-   属性列：
    -   \[column1:string:bad:1001\]
    -   \[column2:integer:128:1002\]
    -   \[column3:double:34.2:1003\]
    -   \[column4:del\_all\_versions\]

编码：

```
<Header开始>[0x75]
<主键列开始>[0x1]
  <Cell1>[0x3][0x4][0x3][3][pk1][0x5][0x3][5][iampk][_cell_checksum]
  <Cell2>[0x3][0x4][0x3][3][pk2][0x5][0x0][8][100][_cell_checksum]
<属性列开始>[0x2]
  <Cell1>[0x3][0x4][0x3][7][column1][0x5][0x3][3][bad][0x7][1001][_cell_checksum]
  <Cell2>[0x3][0x4][0x3][7][column2][0x5][0x0][8][128][0x7][1002][_cell_checksum]
  <Cell3>[0x3][0x4][0x3][7][column3][0x5][0x1][8][34.2][0x7][1003][_cell_checksum]
  <Cell4>[0x3][0x4][0x3][7][column4][0x6][1][_cell_checksum]
[_row_check_sum]
```

