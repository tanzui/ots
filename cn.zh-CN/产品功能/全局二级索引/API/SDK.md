# API/SDK {#concept_jbc_qwf_ffb .concept}

## 创建主表时同时创建索引表 {#section_zxm_xwf_ffb .section}

CreateTable用于创建主表时指定预定义列，同时创建索引表。

后续写入数据后，索引表中将包含主表中所有符合索引构建条件的数据。详情请参见[CreateTable](../../../../cn.zh-CN/.md#)

## 单独创建索引表 {#section_ddx_2xf_ffb .section}

CreateIndex用于在已有的主表上创建索引表。详情请参见[CreateIndex](https://yuque.antfin-inc.com/tablestore/multimodel_database_tablestore/lzrmg3)。

**说明：** 当前版本中使用CreateIndex单独创建的索引表中，不包含主表在执行CreateIndex前已经写入的数据，后续版本会提供支持。

## 删除索引表 {#section_eds_rxf_ffb .section}

DeleteIndex用于删除指定主表下面的指定索引表，此主表上的其它索引表不受影响。详情请参见[DeleteIndex](https://yuque.antfin-inc.com/tablestore/multimodel_database_tablestore/funuhh)。

## 删除主表 {#section_ess_zxf_ffb .section}

DeleteTable用于删除主表，且主表下面的所有索引表都会被删除。详情请参见[DeleteTable](../../../../cn.zh-CN/.md#)。

