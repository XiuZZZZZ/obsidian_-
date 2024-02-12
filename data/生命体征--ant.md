


- 单人生命体征-ant
	- 点
		- 获取左侧病人列表：ward/getWardPatients 
			- Nur.NIS.Service.Base.Ward	GetWardPatients
		- 获取数据：vitalSign/biz/findTempDataByDay
			- Nur.NIS.Service.VitalSign.Temperature	GetTempDataByDay
		- 保存方法：vitalSign/biz/saveObsDataByDay   
			- Nur.NIS.Service.VitalSign.Temperature  SaveObsDataByDay
		- 界面获取事件：Nur.NIS.Service.VitalSign.Event  FindTempEvent

- 业务
	- 点
		- 保存数据：vitalSign/biz/saveObsDataByDay
	- 逻辑