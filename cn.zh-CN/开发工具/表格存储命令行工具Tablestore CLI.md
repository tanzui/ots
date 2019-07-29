# 表格存储命令行工具Tablestore CLI {#concept_34160_zh .concept}

本文主要为您介绍如何使用表格存储命令行工具Tablestore CLI。

## 快速开始 {#section_xpm_m54_dfb .section}

表格存储命令行工具Tablestore CLI是以命令行方式管理表格存储数据的工具，提供简洁、方便的管理命令，支持Windows、Linux、 Mac平台。

 **下载地址** 

-   运行环境
-   Linux/Mac/Windows10
-   binary下载
    -   [aliyun-tablestore-cli-linux-amd64-2018-11-11.zip](https://tablestore-doc.oss-cn-hangzhou.aliyuncs.com/aliyun-tablestore-cli/aliyun-tablestore-cli-linux-amd64-2018-11-11.zip)
    -   [aliyun-tablestore-cli-macos-amd64-2018-11-11.zip](https://tablestore-doc.oss-cn-hangzhou.aliyuncs.com/aliyun-tablestore-cli/aliyun-tablestore-cli-macos-amd64-2018-11-11.zip)
    -   [aliyun-tablestore-cli-windows-amd64-2018-11-11.zip 只支持Win10](https://tablestore-doc.oss-cn-hangzhou.aliyuncs.com/aliyun-tablestore-cli/aliyun-tablestore-cli-windows-amd64-2018-11-11.zip)

 **快速使用** 

``` {#codeblock_ztq_4a8_dwr}
#1 启动命令行工具
$./ts

#2 配置对应的接入信息，请根据实际情况修改对应的字段
ts> config --endpoint https://myinstance.cn-hangzhou.ots.aliyuncs.com --instance myinstance --id test_accessid --key test_accesskey

#3 查看当前实例下的所有表
ts> lt

#4 创建一个全新的表
ts> create -t mysampletable --pk '[{"C":"uid","t":"string"}, {"c":"pid","t":"integer"}]' --ttl 864000 --version 1
'''
创建一张表：mysampletable ，
并指定两列主键，分别是uid(string)和pid(integer)
设置数据声明周期为 864000秒（10天），版本数为1
'''

#5 查看表的信息
ts> desc -t mysampletable

#6 使用该表
ts> use -t mysampletable

#7 插入数据
ts> put --pk '["86", 6771]' --attr '[{"c":"name", "v":"redchen1"}, {"c":"country", "v":"china"}]'
ts> put --pk '["86", 6772]' --attr '[{"c":"name", "v":"redchen2"}, {"c":"country", "v":"china"}]'
ts> put --pk '["86", 6773]' --attr '[{"c":"name", "v":"redchen3"}, {"c":"country", "v":"china"}]'
ts> put --pk '["86", 6774]' --attr '[{"c":"name", "v":"redchen4"}, {"c":"country", "v":"china"}]'

#8 读取数据
ts> get --pk '["86",6771]'
ts> get --pk '["86",6772]'
ts> get --pk '["86",6773]'
ts> get --pk '["86",6774]'

#9 删除表
ts> drop -t mysampletable -y
```

## 查看选项 {#section_y17_6ql_nfv .section}

通过help选项来查看Tablestore CLI支持的所有option。

``` {#codeblock_nee_idn_m7a}
$./ts help

Commands:
  alter                  Alter table 更新表信息
  clear                  Clear the screen
  config                 Config the TableStore access information 配置访问Tablestore的基本信息
  create                 Creat a new table 创建表
  del                    Delete the specify row from TableStore 删除一行数据
  desc                   Show table meta 查询表描述信息
  drop                   Drop the table 删除表
  exit                   Exit the program
  export                 Export the data of table to disk from TableStore, not support multi version 表数据导出磁盘文件，不支持数据多版本
  get                    Get specify row from TableStore 读取一行数据
  help                   Display help
  import                 Load the data to TableStore, not support multi version 磁盘文件数据导入表
  list                   List all tables 列出表名称
  points                 Logically divide the data of the full table into several shards close to the specified size
  press_check            Check data for press 检查压测状态
  press_input            Input data for press 开启压测
  put                    Insert a row to TableStore 插入一行数据
  quit                   Quit the program
  update                 Insert a row to TableStore 更新一行数据
  use                    Select table 选择进行数据操作的表
```

## 初始化 {#section_qk1_zdz_axv .section}

``` {#codeblock_3o8_fcy_0dn}
Sample:
    config --endpoint https://myinstance.cn-hangzhou.ots.aliyuncs.com --instance myinstance --id test_accessid --key test_accesskey
Flags:
      --endpoint Endpoint     TableStore Endpoint
      --id AccessKeyId        User AccessKeyId
      --instance Instance     TableStore Instance
      --key AccessKeySecret   User AccessKeySecret
```

## 表操作 {#section_o1t_k2j_78h .section}

 **创建表** 

``` {#codeblock_mqd_6jj_kg2}
Sample:
    #创建表 第一个主键string类型，第二个主键integer类型
    #第二个主键integer类型，并且设置为自增列
    #设置数据过期时间和最大版本数
    create -t mytable --pk '[{"c":"uid", "t":"string"}, {"c":"pid", "t":"integer"}]'
    create -t mytable --pk '[{"c":"uid", "t":"string"}, {"c":"pid", "t":"integer", "opt":"auto"}]'
    create -t mytable --pk '[{"C":"uid","t":"string"}, {"c":"pid","t":"integer"}]' --ttl 864000 --version 1
Flags:
  -i, --input string           Create table json 支持从配置文件建表
  -p, --pk string              Primary Key
      --read_cu string         read capacity unit (default "0") 读预留cu
  -t, --table string           table name
      --ttl string             Time to live, unit is second, default(-1), means never timeout. (default "-1") 数据过期时间，秒为单位，（默认‘-1’，永不过期）
      --version string         Max version, default(1) (default "1") 数据最大版本数
      --write_cu string        write capacity unit (default "0") 写预留cu
```

 **列出表名称** 

``` {#codeblock_p66_n58_che}
$./ts list
List all table name from instance 列出实例下所有表的名称
```

 **更新表** 

``` {#codeblock_zyc_f44_2z7}
Sample:
    alter -t mytable --ttl 86400 --version 1 --read_cu 0 --write_cu 0
Flags:
      --read_cu string         read capacity unit
  -t, --table string           table name
      --ttl string             Time to live, unit is second
      --version string         Max version
      --write_cu string        write capacity unit
```

 **查询表描述信息** 

``` {#codeblock_iio_rop_ypm}
Sample:
    desc -t tablename
Flags:
  -o, --output string   Output file path
  -t, --table string    Table name
```

 **删除表** 

``` {#codeblock_vu4_bme_90x}
Sample:
    drop -t tablename
Flags:
  -t, --table string   table name
  -y, --yes            confirm yes
```

 **选择进行数据操作的表** 

``` {#codeblock_epq_a1k_hyw}
Sample:
    use -t tablename
Flags:
  -t, --table string   table name
```

## 单行数据操作 {#section_ag9_q60_d5q .section}

 **插入一行数据** 

``` {#codeblock_s58_ovf_iog}
Sample:
    #插入一行数据，第一主键为“86”，第二主键为6771，包含两列，其中第一列列名“name”列值“redchen”
    #选择插入数据时的检查逻辑，1.忽略检查 2.存在相同主键的行允许插入 3.不存在相同主键的行允许插入
    #支持指定时间戳插入
    #主键中有自增列的插入
    #从文件读取数据并插入
    put --pk '["86", 6771]' --attr '[{"c":"name", "v":"redchen"}, {"c":"country", "v":"china"}]'
    put --pk '["86", 6771]' --attr '[{"c":"name", "v":"redchen"}, {"c":"country", "v":"china"}]'  --condition ignore/exist/not_exist
    put --pk '["86", 6771]' --attr '[{"c":"name", "v":"redchen"}, {"c":"country", "v":"china", "ts":15327798534}]'
    put --pk '["86", null]' --attr '[{"c":"name", "v":"redchen"}, {"c":"country", "v":"china"}]'
    put -i /tmp/input
Flags:
      --attr string        Attribute column value
      --condition string   input condition, ignore/exist/not_exist (default "ignore")
  -i, --input string       input file
      --pk string          PrimaryKey value
```

 **读取一行数据** 

``` {#codeblock_qct_ruf_88k}
Sample:
    get --pk '["86",6771]'
Flags:
  -o, --output string   Output file path
      --pk string       Input primary key column value
```

 **更新一行数据** 

``` {#codeblock_f2v_26o_efn}
Sample:
    update --pk '["86", 6771]' --attr '[{"c":"name", "v":"redchen"}, {"c":"country", "v":"china"}]'
    update --pk '["86", 6771]' --attr '[{"c":"name", "v":"redchen"}, {"c":"country", "v":"china"}]'  --condition ignore/exist/not_exist
    update -i /tmp/input
Flags:
      --attr string        Attribute column value
      --condition string   input condition, ignore/exist/not_exist (default "ignore")
  -i, --input string       input file
      --pk string          PrimaryKey value
```

 **删除一行数据** 

``` {#codeblock_sqd_sq3_8wo}
Sample:
    del --pk '["86", 6771]'
Flags:
      --pk string   PrimaryKey value
```

## 简单压测操作 {#section_evz_8ck_57z .section}

 **开启压测** 

``` {#codeblock_qth_coa_835}
Sample:
        #创建一张表，第一主键为“分区”，第二主键为“行序号”
        #使用该表，向分区“redchen”插入10条数据
        create -t mytable --pk '[{"c":"uid", "t":"string"}, {"c":"pid", "t":"integer"}]'
        use -t mytable
        press_input --part redchen --count 10
Flags:
      --begin string   Begin index, default 0 从指定行序号开始插入
      --count string   Input count , length of single row is 1KB 插入的行数，每行数据长度为1KB
      --part string    Partition key, default 'redchen' (default "redchen")
```

 **检查压测状态** 

``` {#codeblock_p1f_ak7_4ov}
Sample:
        create -t mytable --pk '[{"c":"uid", "t":"string"}, {"c":"pid", "t":"integer"}]'
        use -t mytable
        press_check --part redchen --begin 0 --count 1000
Flags:
      --begin string   Begin index, default 0
      --count string   Check count
      --part string    Partition key
  -y, --yes            show cost time
```

## 数据备份操作 {#section_nsp_5e4_kdq .section}

 **表数据导出磁盘文件** 

``` {#codeblock_wob_3oj_tps}
Sample:
    #导出当前表的全部数据到磁盘文件
    #导出指定列数据
    export -o /tmp/mydata
    export -o /tmp/mydata -c attr1,attr2,attr3
Flags:
  -c, --columns string   Column names
  -o, --output string    Output file path
```

 **磁盘文件数据导入表** 

``` {#codeblock_byg_q17_xos}
Sample:
    #读取磁盘文件数据导入表
    #忽略时间戳检查，使用当前时间
    import -i /tmp/mydata
    import -i /tmp/mydata --ignore_ts
    '''
    file format:
    {"PK":{"Values":["redchen",0]},"Attr":{"Values":[{"C":"country","V":"china0"},{"C":"name","V":"redchen0"}]}}
    {"PK":{"Values":["redchen",1]},"Attr":{"Values":[{"C":"country","V":"china1"},{"C":"name","V":"redchen1"}]}}
    '''
Flags:
      --ignore_ts      ignore timestamp
  -i, --input string   Input file name
```

