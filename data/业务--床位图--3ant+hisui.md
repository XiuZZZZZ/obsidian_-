* 床位图-ant-hisui
* 业务点
	* 右键点击
		* 入口  src/page/bedChart/index.vue  handleMenuItem
		* 
* 逻辑点
	* 获得左侧区：Nur.NIS.Service.Base.Ward  GetRooms
	* 分床：
		* 获取主管医生：Nur.NIS.Service.Base.Ward  GetMainDoctors
		* 获取主管护士：Nur.NIS.Service.Base.Ward  GetMainNursesByWardID
		* 分床后台方法：Nur.NIS.Service.Transaction:TransToBed
	* 分等候区
	* 判断是否首次分床：Nur.NIS.Service.Transaction:IfFirstDispatchBed
	* 转科
		* 转科csp：nur.hisui.transferdept.csp
		* 转科是否开了转科医嘱：Nur.NIS.Service.Base.Patient：IfHasTransOrd
		* 获取转科科室：Nur.NIS.Service.Base.Loc  GetLocsExceptAdmLoc
		* 转科后台方法：Nur.NIS.Service.Base.Transfer  TransLocApply
	* 转病区
	* 获得
		* 主管医生护士：
		* 主管医生护士：
	* 设置
		* 主管医生护士：
		* 主管医生护士：
	* 打印入口：BedCommonPrint
	* 详细病人信息卡csp：
      *