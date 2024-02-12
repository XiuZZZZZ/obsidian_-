[[老板护理病历表结构]]

- 护理病历-老板
	- 业务点
		- csp：dhcnuremrlistnewIP.csp
		- 获得目录方法：[[老板护理病历获得目录方法]]
		- 病人信息：Nur.DoctorOrderSheet  getPatLocs  getPatInfo
		- 记录单保存方法：Nur.DHCNurseRecSub  Save
		- 记录单获取数据方法：web.DHCNurHCRecComm  GetCareRecComm
		- 评估单保存方法：
		- 打印获取后台数据方法：web.DHCNurRecPrint  GetCareRecComm  web.DHCNUREMR GetPrintData
			- ##class(%ResultSet).RunQuery("web.DHCNurRecPrint","GetCareRecComm",^objcyfff)
	- 需求
		- [[ 一个评估单上的按钮，跳转两个评估单录入界面]]
		- [[不能保存特殊字符]]
		- [[不打印未勾选的checkbox]]
	- 常用方法
		- [[获得记录单值，指定人，指定单子，指定item]]
		- [[获取评估单值]]