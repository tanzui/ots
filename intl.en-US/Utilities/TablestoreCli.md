# TablestoreCli {#concept_34160_zh .concept}

## Quick start {#section_xpm_m54_dfb .section}

TablestoreCli is a command-line interface \(CLI\) tool used to manage data in Table Store. This tool provides simple and clear commands and supports Windows, Linux, and Mac operating systems.

 **Downloads** 

-   Running environment
-   TablestoreCli can run in Linux, Mac, and Windows 10 operating systems.
-   Download the following binary versions:
    -   [aliyun-tablestore-cli-linux-amd64-2018-11-11.zip](https://tablestore-doc.oss-cn-hangzhou.aliyuncs.com/aliyun-tablestore-cli/aliyun-tablestore-cli-linux-amd64-2018-11-11.zip)
    -   [aliyun-tablestore-cli-macos-amd64-2018-11-11.zip](https://tablestore-doc.oss-cn-hangzhou.aliyuncs.com/aliyun-tablestore-cli/aliyun-tablestore-cli-macos-amd64-2018-11-11.zip)
    -   [aliyun-tablestore-cli-windows-amd64-2018-11-11.zip, only applicable to Windows 10.](https://tablestore-doc.oss-cn-hangzhou.aliyuncs.com/aliyun-tablestore-cli/aliyun-tablestore-cli-windows-amd64-2018-11-11.zip)

 **Get started** 

``` {#codeblock_ztq_4a8_dwr}
#1 Start TablestoreCli.
$./ts

#2 Configure the corresponding connection information and modify the fields according to actual conditions.
ts> config --endpoint https://myinstance.cn-hangzhou.ots.aliyuncs.com --instance myinstance --id test_accessid --key test_accesskey

#3 View all tables on the current instance.
ts> lt

#4 Create a new table.
ts> create -t mysampletable --pk '[{"C":"uid","t":"string"}, {"c":"pid","t":"integer"}]' --ttl 864000 --version 1
'''
Create a table: mysampletable,
and specify two primary keys: uid(string) and pid(integer).
Set the data declaration cycle to 864,000 seconds or 10 days and the number of versions to 1.
'''

#5 View table information.
ts> desc -t mysampletable

#6 Use this table.
ts> use -t mysampletable

#7 Insert data into the table.
ts> put --pk '["86", 6771]' --attr '[{"c":"name", "v":"redchen1"}, {"c":"country", "v":"china"}]'
ts> put --pk '["86", 6772]' --attr '[{"c":"name", "v":"redchen2"}, {"c":"country", "v":"china"}]'
ts> put --pk '["86", 6773]' --attr '[{"c":"name", "v":"redchen3"}, {"c":"country", "v":"china"}]'
ts> put --pk '["86", 6774]' --attr '[{"c":"name", "v":"redchen4"}, {"c":"country", "v":"china"}]'

#8 Read data from the table.
ts> get --pk '["86",6771]'
ts> get --pk '["86",6772]'
ts> get --pk '["86",6773]'
ts> get --pk '["86",6774]'

#9 Delete the table.
ts> drop -t mysampletable -y
```

## View supported options {#section_y17_6ql_nfv .section}

You can use the help option to view all options that TablestoreCli supports.

``` {#codeblock_nee_idn_m7a}
$./ts help

Commands:
  alter                  Alter table
  clear                  Clear the screen 
  config                 Config the TableStore access information
  create                 Creat a new table
  del                    Delete the specify row from TableStore
  desc                   Show table meta
  drop                   Drop the table
  exit                   Exit the program
  export                 Export the data of table to disk from TableStore, not support multi version
  get                    Get specify row from TableStore
  help                   Display help
  import                 Load the data to TableStore, not support multi version
  list                   List all tables
  points                 Logically divide the data of the full table into several shards close to the specified size 
  press_check            Check data for press
  press_input            Input data for press
  put                    Insert a row to TableStore
  quit                   Quit the program 
  update                 Insert a row to TableStore
  use                    Select table
```

## Initialization {#section_qk1_zdz_axv .section}

``` {#codeblock_3o8_fcy_0dn}
Sample:
    config --endpoint https://myinstance.cn-hangzhou.ots.aliyuncs.com --instance myinstance --id test_accessid --key test_accesskey
Flags:
      --endpoint Endpoint     TableStore Endpoint
      --id AccessKeyId        User AccessKeyId
      --instance Instance     TableStore Instance
      --key AccessKeySecret   User AccessKeySecret
```

## Table-level operations {#section_o1t_k2j_78h .section}

 **Create a table** 

``` {#codeblock_mqd_6jj_kg2}
Sample:
    # Create a table that contains the first primary key of string type and the second primary key of integer type.
    # Specify the second primary key as an auto-increment column of integer type.
    # Set Time To Live (TTL) and Max Versions.
    create -t mytable --pk '[{"c":"uid", "t":"string"}, {"c":"pid", "t":"integer"}]'
    create -t mytable --pk '[{"c":"uid", "t":"string"}, {"c":"pid", "t":"integer", "opt":"auto"}]'
    create -t mytable --pk '[{"C":"uid","t":"string"}, {"c":"pid","t":"integer"}]' --ttl 864000 --version 1
Flags:
  -i, --input string           Create table json
  -p, --pk string              Primary Key 
      --read_cu string         read capacity unit (default "0")
  -t, --table string           table name 
      --ttl string             Time to live, unit is second, default(-1), means never timeout. (default "-1")
      --version string         Max version, default(1) (default "1")
      --write_cu string        write capacity unit (default "0")
```

 **List table names** 

``` {#codeblock_p66_n58_che}
$./ts list
List all table name from instance
```

 **Update the table** 

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

 **Query the description of the table** 

``` {#codeblock_iio_rop_ypm}
Sample:
    desc -t tablename
Flags:
  -o, --output string   Output file path
  -t, --table string    Table name
```

 **Delete the table** 

``` {#codeblock_vu4_bme_90x}
Sample:
    drop -t tablename
Flags:
  -t, --table string   table name
  -y, --yes            confirm yes
```

 **Select a table for data operations** 

``` {#codeblock_epq_a1k_hyw}
Sample:
    use -t tablename
Flags:
  -t, --table string   table name
```

## Single-row operations {#section_ag9_q60_d5q .section}

 **Insert a row into the table** 

``` {#codeblock_s58_ovf_iog}
Sample:
    #Insert a row into the table. This row contains two primary keys and two attribute columns. The first primary key is “86”, and the second primary key is 6771. The first attribute column is named as “name” and the attribute value is “redchen”.
    # Select a check logic for inserting the row: 1. Ignore the check. 2. Allow inserting the row that contains the same primary key. 3. Allow inserting the row that does not contain the same primary key.
    # Allow inserting the specified timestamp.
    # Allow inserting the row that contains an auto-increment column as the primary key.
    # Read and insert data from a file.
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

 **Read a row** 

``` {#codeblock_qct_ruf_88k}
Sample:
    get --pk '["86",6771]'
Flags:
  -o, --output string   Output file path 
      --pk string       Input primary key column value 
```

 **Update a row** 

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

 **Delete a row** 

``` {#codeblock_sqd_sq3_8wo}
Sample:
    del --pk '["86", 6771]'
Flags:
      --pk string   PrimaryKey value
```

## Simple stress testing {#section_evz_8ck_57z .section}

 **Enable stress testing** 

``` {#codeblock_qth_coa_835}
Sample:
        # Create a table. In the table, the first primary key is "Partition", and the second primary key is "Row number".
        # Use this table to insert 10 rows into Partition "redchen".
        create -t mytable --pk '[{"c":"uid", "t":"string"}, {"c":"pid", "t":"integer"}]'
        use -t mytable
        press_input --part redchen --count 10
Flags:
      --begin string   Begin index, default 0
      --count string   Input count , length of single row is 1KB
      --part string    Partition key, default 'redchen' (default "redchen") 
```

 **Check the stress testing status** 

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

## Data backup {#section_nsp_5e4_kdq .section}

 **Export data from a table to a disk file** 

``` {#codeblock_wob_3oj_tps}
Sample:
    # Export all data from the current table to a disk file.
    # Export data from the specified column.
    export -o /tmp/mydata
    export -o /tmp/mydata -c attr1,attr2,attr3
Flags:
  -c, --columns string   Column names
  -o, --output string    Output file path
```

 **Import data from a disk file to a table** 

``` {#codeblock_byg_q17_xos}
Sample:
    # Read data from a disk file and export the data to a table.
    # Ignore the timestamp check and use the current time.
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

