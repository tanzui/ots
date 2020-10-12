# 使用BatchWriteRow一次提交100条数据的时候报OTSParameterInvalid错误

## 现象

使用BatchWriteRow一次提交100条数据时出现以下错误。

```
ErrorCode: OTSParameterInvalid, ErrorMessage: The input parameter is invalid.
```

## 原因

因为一次batch操作不能有重复行，如果有重复行，则会报错。

## 解决方案

将100条改为1条提交一次，其他代码不变，即可提交成功。

