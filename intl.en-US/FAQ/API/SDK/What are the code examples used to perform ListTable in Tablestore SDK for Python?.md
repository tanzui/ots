# What are the code examples used to perform ListTable in Tablestore SDK for Python?

Examples

```



import time

import logging

import unittest

from ots2 import *

ENDPOINT = "https://xxx.cn-hangzhou.ots.aliyuncs.com";

ACCESSID = "xxx";

ACCESSKEY = "xxx";

INSTANCENAME = "xxx";

ots_client = OTSClient(ENDPOINT, ACCESSID, ACCESSKEY, INSTANCENAME)

list_response = ots_client.list_table()

print u'instance table:'

for table_name in list_response:

    print table_name
		
```

**Note:** For more information about how to install and use Tablestore SDK for Python, see [Preface](/intl.en-US/SDK Reference/Python SDK/Preface.md) in Tablestore SDK for Python.

Tablestore SDK for Python does not provide import at the beginning of each snippet for you to run code. If you do not add import to the beginning of each snippet when you run the code, a similar output is displayed:

```
Traceback (most recent call last):

  File "listtable.py", line 6, in

    ots_client = OTSClient(ENDPOINT, ACCESSID, ACCESSKEY, INSTANCENAME)

NameError: name 'OTSClient' is not defined

		
```

Add import to run the code.

![](http://docs-aliyun.cn-hangzhou.oss.aliyun-inc.com/assets/pic/38587/cn_zh/1512542463975/38587-01.png)

