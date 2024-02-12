

- 概述
- 过程
	- 找到获得医嘱名称的方法  ##class(Nur.NIS.Service.OrderSheet.Sheet).getOrdName(178,455)
	- 修改成下面即可 



```
    s ordNameObj=##Class(Nur.NIS.Service.OrderSheet.OrderName).%New(OeordId, OeordSub, TYPE, IfShowAllCY)
    i (ordNameObj.arcimCommonName()["手术申请")&&(arcitmId="10217||1") {
	    s poId =$o(^CIS.AN.OperScheduleI("IOrderItem"," "_OeordId_"||"_OeordSub,""),-1)
		i poId'=""{
			q "手术申请"_" "_##class(CIS.AN.BL.OperScheduleList).GetAnaestMethod(poId)
		}else{
			q "手术申请"
		}
	
	}
```