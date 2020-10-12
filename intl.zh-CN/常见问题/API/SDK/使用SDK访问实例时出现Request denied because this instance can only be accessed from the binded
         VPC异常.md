# 使用SDK访问实例时出现Request denied because this instance can only be accessed from the binded VPC异常

介绍使用SDK访问实例时出现Request denied because this instance can only be accessed from the binded VPC异常的现象、原因和解决方案。

## 现象

使用SDK访问实例时出现如下异常：

```
[ErrorCode]:OTSAuthFailed, [Message]:Request denied because this instance can only be accessed from the binded VPC.
```

## 原因

实例绑定VPC后，只能在绑定的VPC中使用VPC访问地址访问该实例。

## 解决方案

实例绑定VPC后，使用SDK访问实例时，请在绑定的VPC中使用VPC访问地址进行访问。

通过控制台在实例的网络管理页签可以获取实例的VPC访问地址，如下图所示。

![fig_vpc](https://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/zh-CN/9868958951/p133644.png)

