# PlainBuffer {#reference3003 .reference}

The PlainBuffer format is defined in Table Store to indicate row data, because it delivers better performance for serialization, and small object resolution, compared to Protocol Buffer.

## Format definition {#section_cvs_nsq_dfb .section}

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

## Tag value { .section}

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

## ValueType value { .section}

The values of value\_type in formated\_value are as follows:

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

## Calculate the checksum { .section}

The basic logic for calculating the checksum is as follows:

-   Calculate the name/value/type/time stamp of each cell.
-   Calculate the delete in the row. If delete mark exists in the row, supplement a single-byte 1; otherwise, supplement a single-byte 0.
-   Because the checksum is calculated for each cell, the cell checksum is used to calculate the row checksum, that is, the CRC operation is only performed on the checksums of cells in the row, not data in the row.

C++ implementation:

```language-c++
void GetChecksum(uint8_t* crc, const InplaceCell& cell)
{
	Crc8(crc, cell.GetName());
	Crc8(crc, cell.GetValue(). GetInternalSlice());
	Crc8(crc, cell.GetTimestamp());
	Crc8(crc, cell.GetOpType());
}

void GetChecksum(uint8_t* crc, const InplaceRow& row)
{
	const std::deque<InplaceCell>& pk = row.GetPrimaryKey();
	for (size_t i = 0; i < pk.size(); i++) {
		uint8_t* cellcrc;
		*cellcrc = 0;
		GetChecksum(cellcrc, pk[i]);
		Crc8(crc, *cellcrc);
	}
	for (int i = 0; i < row.GetCellCount(); i++) {
		uint8_t* cellcrc;
		*cellcrc = 0;
		GetChecksum(cellcrc, row.GetCell(i));
		Crc8(crc, *cellcrc);
	}
	
	uint8_t del = 0;
	if (row.HasDeleteMarker()) {
		del = 1;
	}
	Crc8(crc, del);
}

```

## Example { .section}

A data row contains two primary key columns and four data columns. The primary key types are string and integer, and the data types are string, int, and double. The versions are 1001, 1002, and 1003. A column is also contained to delete all versions.

-   Primary key column:
    -   \[pk1:string:iampk\]
    -   \[pk2:integer:100\]
-   Attribute column:
    -   \[column1:string:bad:1001\]
    -   \[column2:integer:128:1002\]
    -   \[column3:double:34.2:1003\]
    -   \[column4:del\_all\_versions\]

Encoding:

```
	<Header starting>[0x75]
	<Primary key column starting>[0x1]
		<Cell1>[0x3][0x4][3][pk1][0x5][3][5][iampk]
		<Cell2>[0x3][0x4][3][pk2][0x5][0][100]
	<Attribute column starting>[0x2]
		<Cell1>[0x3][0x4][7][column1][0x5][0x3][3][bad][0x7][1001]
		<Cell2>[0x3][0x4][7][column2][0x5][0x0][128][0x7][1002]
		<Cell3>[0x3][0x4][7][column3][0x5][0x1][34.2][0x7][1003]
		<Cell4>[0x3][0x4][7][column4][0x5][0x6][1]

```

