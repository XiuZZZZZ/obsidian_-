

- 概述
	- 理解认识护士执行ca签名干了什么
	- 提供方法，pda那边也一样干这样的事情就行了
- 过程
	- 提供方法（如下）


```

【保存pda的ca签名数据】
/// ##class(Nur.Custom.NeedUnit.NeedUnit231025).CaSign(CurrOrderItemStr, UserID, SignTimeStamp, OperationType, OrdItemsHashVal, MainSignCert, MainSignValue, DeviceCode)
/// 参数1：医嘱执行记录串，用^分隔多个医嘱执行记录
/// 参数2：用户id
/// 参数3：时间搓，SignTimeStamp
/// 参数4：1代表执行，2代表撤销执行
/// 参数5：// 平台返回的数据：对医嘱xml处理的hash值
/// 参数6：// 平台返回的数据：ca用户唯一标识 
/// 参数7：// 平台返回的数据：对医嘱数据近行签名操作
/// 参数8：PDA，PC。其中一个
```

