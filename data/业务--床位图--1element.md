
- 业务点
	* 分床：
	* 分等候区
	* 判断是否首次分床：
	* 转科：
	* 快速调试转科：Nur.CommonInterface.ADTDischarge  ifHasTransOrd  返回0即可
	* 转科获取科室：Nur.CommonInterface.Loc  getLocsExceptAdmLoc
	* 转科获取病区：Nur.CommonInterface.Loc  getTransLocLinkWards
	* 转科后台方法：Nur.NIS.Service.Base.Transfer  TransLocApply
	* 转病区：
	* 转病区获取病区方法：Nur.CommonInterface.Loc  getTransLocLinkWardsByAdm
	* 转病区后台方法：Nur.CommonInterface.Bed  transWard
	* 获得主管医生护士：Nur.NIS.Service.Base.Ward  GetMainDoctors  GetMainNursesByWardID
	* 设置主管医生护士：
	* 床位图打印：
	* 详细病人信息卡：
	* 图标路径：/dthealth/app/dthis/web/images/webemr