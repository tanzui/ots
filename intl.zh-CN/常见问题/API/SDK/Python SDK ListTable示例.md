# Python SDK ListTable示例

ListTable代码示例

```



import time

import logging

import unittest

from ots2 import *

ENDPOINT = “https://xxx.cn-hangzhou.ots.aliyuncs.com“;

ACCESSID = “xxx”;

ACCESSKEY = “xxx”;

INSTANCENAME = “xxx”;

ots_client = OTSClient(ENDPOINT, ACCESSID, ACCESSKEY, INSTANCENAME)

list_response = ots_client.list_table()

print u’instance table:’

for table_name in list_response:

    print table_name
		
```

**说明：** Python SDK的安装和操作，请参见[Python SDK](/intl.zh-CN/SDK 参考/Python SDK/前言.md)。

Python SDK文档中没有import的提示，如不加import会出现如下提示。

```
Traceback (most recent call last):

  File “listtable.py”, line 6, in

    ots_client = OTSClient(ENDPOINT, ACCESSID, ACCESSKEY, INSTANCENAME)

NameError: name ‘OTSClient’ is not defined

		
```

添加import运行即可。

![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/38587/cn_zh/1512542463975/38587-01.png)

