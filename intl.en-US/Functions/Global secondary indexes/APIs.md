# APIs {#concept_jbc_qwf_ffb .concept}

## CreateTable {#section_zxm_xwf_ffb .section}

You can call the CreateTable method to create a table, and an index with pre-defined attributes at the same time.

When you write data to a base table, an index on this base table is also updated. For more information, see [CreateTable](../../../../intl.en-US/API Reference/Operations/CreateTable.md#).

## CreateIndex {#section_ddx_2xf_ffb .section}

You can call the CreateIndex method to create an index on a base table.

**Note:** The current version does not support copying existing base table data to the index when you call the CreateIndex method to create an index on a base table. This feature will be supported by later versions.

## DeleteIndex {#section_eds_rxf_ffb .section}

You can call the DeleteIndex method to delete indexes on a base table. The other indexes on this table will not be affected.

## DeleteTable {#section_ess_zxf_ffb .section}

You can call the DeleteTable method to delete a base table and all indexes on this table. For more information, see [DeleteTable](../../../../intl.en-US/API Reference/Operations/DeleteTable.md#).

