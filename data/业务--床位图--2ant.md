* 业务点
  * 左侧等候区数据
  * ward/getRooms  Nur.NIS.Service.Base.Ward  GetRooms
  * 判断是否首次分床：Nur.NIS.Service.Transaction  IfFirstDispatchBed
  * 分床：
  * 获取护士列表：Nur.NIS.Service.Base.Ward  GetMainDoctors
  * 分床后台方法：Nur.NIS.Service.Transaction  TransToBed
  * 分等候区
  * 判断是否首次分床：
  * 转科：
  * 转科获取科室：Nur.NIS.Service.Base.Loc  GetLocsExceptAdmLoc
  * 转病区
  * 转病区后台方法：Nur.NIS.Service.Base.Transfer	TransWard
  * 获得主管医生护士：Nur.NIS.Service.Base.Ward  GetMainDoctors  GetMainNursesByWardID
  * 设置主管医生护士：
  * 打印入口：BedCommonPrint
  * 详细病人信息卡：
  * 其它
  * 获取护理级别：##class(Nur.NIS.Service.Base.Patient).GetCareLevel(EpisodeID)