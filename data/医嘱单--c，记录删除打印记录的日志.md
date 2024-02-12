


## main


- 修改
	- 找到删除的后台方法，及其c#方法
		- 后台方法
			- Nur.DoctorOrderPrintRecord  deletePrintRecord
			- Nur.DoctorOrderPrintRecord  deleteLocPrintRecord
		- 前台方法
	- 增加的log
		- ^zhu("log","医嘱单","deletePrintRecord",Adm,currDateH,currTimeH)=$lb(userId,Adm, OrderType)
		- ^zhu("log","医嘱单","deleteLocPrintRecord",adm,currDateH,currTimeH)=$lb(userId, adm, orderType, locNo, startPage)