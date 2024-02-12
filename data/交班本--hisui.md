

- 交班本-hisui
	- 业务点
		- csp：nur.shift.index.csp
		- 上面整体的数据：Nur.SHIFT.Service.ShiftBizV2   QueryGroupByItemDR
		- 上面项目点击后的数据：Nur.SHIFT.Service.ShiftBizV2
		- 中间统计区：Nur.SHIFT.Service.ShiftBizV2  GetMiddleInfo
		- 下面病人的数据：Nur.SHIFT.Service.ShiftBizV2  QueryShiftDetailList
		- 下面左边数据：##class(Nur.SHIFT.Service.ShiftBizV2).GetShiftItem(WardID)
		- 保存数据：Nur.SHIFT.Service.ShiftBizV2  SavePatEdit
		- 打印获取数据：Nur.SHIFT.Service.Emr.EmrService  QueryShiftDetailListV2
		- 引用w方式
		- 项目配置
			- 统计方法：返回1代表统计  返回3代表失效了
			- 统计规则：如果判断为3，则会取消项目。如果为1则会加上项目
		- 班次打印方法
			- ##class(Nur.SHIFT.Service.Emr.EmrService).GetDayV2("1^58^13^2023-11-13^@@^@@^Y^")
			- ##class(Nur.SHIFT.Service.Emr.EmrService).GetSmallNightV2("1^58^13^2023-11-13^@@^@@^Y^")
			- ##class(Nur.SHIFT.Service.Emr.EmrService).GetBigNightV2("1^58^13^2023-11-13^@@^@@^Y^")
		- 配置
			- 数据引入的csp：nur.hisui.shiftSetDataImport.csp
			- 点击上面，获取下面数据：Nur.SHIFT.Service.ShiftSetting  GetShiftEmrDataFieldList
		- 引用多个csp
			- 引用按钮id：btn_intro
			- 引用绑定方法：referHandler
			- 引用打开csp：nur.hisui.shiftLog.csp
			- 引用框架id：iframeRefer
			- 确定引用：js/sureReferHandle
		- 项目配置
			- 配置带入数据[[配置带入数据]]
	- 需求
	- 常用方法
		- [[获取指定交班日期，指定班次，指定病人的所有项目]]






## 常见问题

- 交班本内容全显示
	- [[交班本--hisui，班次全部显示.canvas]]
- 