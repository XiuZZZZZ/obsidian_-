

## main

- 过程
	- 确定医嘱
	- 确定业务，pdd医嘱不需要阴性阳性都可以通过
- 修改
	- 定位地方  findDurg的里面
	- 修改方法，添加内容： ##class(Nur.IP.DrugAudit).getDrugErrorA(16)
		- s ifPPd = ##class(Nur.NIS.Service.Base.OrderHandle).ifPPDOrder(oeoreID)
		- q:ifPPd="Y" ""