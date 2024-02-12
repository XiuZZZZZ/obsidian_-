

- 概述
- 过程
	- 护士执行csp：dhc.nurse.vue.orderexcute.csp
	- 左侧病人列表：nur.hisui.common.patientlistnew.csp
	- 找到tree初始化的地方，并修改：initPatientTree
		- 初始化的地方修改两行代码
			- `checkbox: _PatListGV.ifMultiCheck==1 ? true : false,  `
			- `cascadeCheck:false,`
		- 增加方法  [[增加方法，递归勾选]]
		- 修改onCheck  [[修改onCheck方法]]