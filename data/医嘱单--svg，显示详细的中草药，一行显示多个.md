## 过程

- 过程
	- 现状
		- 定位病人数据  中医科住院，登记号0004017210，临时医嘱 [[#现状]]
	- 修改
		- 添加方法  [[#GetGroupOrderIdStr]]
		- 增加方法  [[#getCYName2]]
		- 修改方法  [[#getOrdName]]
		- 修改获得医嘱方法的行 [[#insertOrderList]]
	- 修改后
		- [[#修改后]]
	

## 修改后
![[Pasted image 20231219225422.png]]

## 现状

![[Pasted image 20231219224715.png]]
## getCYName2
```
/// Nur.NIS.Service.OrderSheet.Sheet
ClassMethod getCYName2(OeordId, OeordSub, CONFIG As %ArrayOfDataTypes = "")
{
	
	i (OeordId_"||"_OeordSub="418||15"){
		s ^temp("getCYName","CONFIG")=##class(NurMp.ErrorMsg).Encode(CONFIG)
		s ^temp("getCYName")=$lb(OeordId, OeordSub, ^temp("getCYName","CONFIG"))
		s CONFIG = ##class(Nur.NIS.Common.JSON).Decode(CONFIG)
	}
	s ordName=""
	s ifGrassDrug=##class(Nur.NIS.Service.OrderSheet.Filter).ifGrassDrug(OeordId, OeordSub)
	q:(ifGrassDrug'="Y") ordName
	// 获取主医嘱
	s currOrderId = OeordId_"||"_OeordSub
	s mainOrderId = $p($g(^OEORD(OeordId,"I",OeordSub,11)),"^",39)
	i mainOrderId'=""{
		s childOrderIdStr = ##class(Nur.Z.NeedUnit.OrderSheet).GetGroupOrderIdStr(+mainOrderId,$p(mainOrderId,"||",2))
	}else{
		s childOrderIdStr = ##class(Nur.Z.NeedUnit.OrderSheet).GetGroupOrderIdStr(OeordId,OeordSub)
	}
	
	s num = 0
	s canNext = 0
	f i=1:1:$l(childOrderIdStr,"^"){
		q:num=6
		s orderId = $p(childOrderIdStr,"^",i)
		s orderRid = $p(orderId,"||",1)
		s orderSid = $p(orderId,"||",2)
		i orderId=currOrderId s canNext = 1
		
		i canNext=1{
			s num = num + 1
		    s arcimID =$p($g(^OEORD(orderRid,"I",orderSid,1)),"^",2)
			i arcimID'="" s arcimDesc= $P($G(^ARCIM(+arcimID,$p(arcimID,"||",2),1)),"^",2)
			s arcimCommonName=$P($G(^ARCIM(+arcimID,$p(arcimID,"||",2),1)),"^",2)
			s Phcdf=$P($g(^ARCIM(+arcimID,$P(arcimID,"||",2),1)),"^",12)
			if Phcdf'="" s BillUOMRowid=$p(^PHCD(+Phcdf,"DF",$p(Phcdf,"||",2),2),"^",4),BillUOMDesc=##class(web.DHCDocOrderCommon).GetUOMDesc(BillUOMRowid)
			s doseQty = $p($g(^OEORD(orderRid,"I",orderSid,2)),"^",1)
			s arcimCommonName = arcimCommonName_" "_doseQty_BillUOMDesc
			s ordName = $s(ordName="":arcimCommonName,1:ordName_" "_arcimCommonName)
		}
	}
	
	
	

	q ordName
}
```

## getOrdName

```
/// Nur.NIS.Service.OrderSheet.Sheet
ClassMethod getOrdName(OeordId, OeordSub, suffixConfig As %ListOfDataTypes = "", TYPE = "", showOrdNameConfig As %ArrayOfDataTypes = "", CONFIG As %ArrayOfDataTypes = "") As %ListOfDataTypes
{
	s ^temp("getOrdName")=$lb(OeordId, OeordSub, suffixConfig,TYPE,showOrdNameConfig,CONFIG)
    s ordName=""
    s arcitmId=..OEOrdItem(OeordId,OeordSub).itmMastDR()
    s itemcatId=..ARCItmMast(+arcitmId,$p(arcitmId,"||",2)).itemCatDR()
    s priorityId=..OEOrdItem(OeordId,OeordSub).priorDR()
    if $IsObject(showOrdNameConfig)&&(showOrdNameConfig.Define(arcitmId))
    {
	    q showOrdNameConfig.GetAt(arcitmId)
	}
	
	if $IsObject(showOrdNameConfig)&&(showOrdNameConfig.Define(itemcatId))
    {
	    q showOrdNameConfig.GetAt(itemcatId)
	}
	if (suffixConfig="")
	{
		//如果为空则取所有的配置字段，方便调试
		s suffixConfig=##class(Nur.NIS.Service.OrderSheet.OrderName).getCurrentMethod()
	}
	
	//中草药显示格式目前写死
	s ordName=..getCYName2(OeordId,OeordSub,CONFIG)
	q:(ordName'="") ordName
	
	s IfShowAllCY="N"
	if ($IsObject(CONFIG))
	{
		s IfShowAllCY=CONFIG.GetAt("IfShowAllCY")
	}
    s ordNameObj=##Class(Nur.NIS.Service.OrderSheet.OrderName).%New(OeordId, OeordSub, TYPE, IfShowAllCY)
    
    if (suffixConfig.Count()=0)
    {
    	q ordNameObj.arcimCommonName()
    }else{
		    s index=0
	    	while(index<suffixConfig.Count())
	    	{
		    	#dim single as %ArrayOfDataTypes
		    	s single=suffixConfig.GetNext(.index)
		    	s code=single.GetAt("Code")
		    	s Prioritys=single.GetAt("Prioritys")
		    	s ArcCats=single.GetAt("ArcCats")
		    	continue:(Prioritys'="")&&((","_Prioritys_",")'[(","_priorityId_","))
		    	continue:(ArcCats'="")&&((","_ArcCats_",")'[(","_itemcatId_","))
		    	s itemId=$o(^CT.NUR.NIS.NurseBasicDataI("Code"," 1"," "_$ZConvert(code,"U"),""),-1)
		    	if (##class(CT.NUR.NIS.NurseBasicData).%ExistsId(itemId))
		    	{
			    	s itemObj=^CT.NUR.NIS.NurseBasicDataD(itemId)
			    	s expression=$lg(itemObj,6)
			    	s cName=$p(expression,":",1)
			    	s mName=$p(expression,":",2)
			    	continue:(cName="")||(mName="")
			    	s expression="##class("_cName_")."_mName_"(oeoriId)"
					s tmpexe="(oeoriId) SET out="_expression_" QUIT out"
					s value=$XECUTE(tmpexe,OeordId_"||"_OeordSub)
					continue:(value="")
		    		s ordName=$CASE(ordName,"":value,:(ordName_" "_value))
			    	
			    }
			}	
	}
	
	s lisOtherCode = ##class(LIS.WS.BLL.OBTTestCode).QryTestCodeAbbrByHISID(arcitmId)
	i lisOtherCode[":("  s ordName = ordName_lisOtherCode
	
	
    q ordName
}

```

## GetGroupOrderIdStr
```

/// ##class(Nur.Z.NeedUnit.OrderSheet).GetGroupOrderIdStr(418,15)
ClassMethod GetGroupOrderIdStr(orderRid, orderSid)
{
	s groupOrderIdStr = ""
    s child=0  
    f 
	{
		s child=$O(^OEORDi(0,"OEORI",orderRid,orderRid_"||"_orderSid,child))
		q:child=""
		s groupOrderIdStr = $s(groupOrderIdStr="":orderRid_"||"_child,1:groupOrderIdStr_"^"_orderRid_"||"_child)
	}
	s groupOrderIdStr = orderRid_"||"_orderSid_"^"_groupOrderIdStr
    q groupOrderIdStr
}
```

## insertOrderList

```
    					s ifGrassFlag = ##class(Nur.NIS.Service.OrderSheet.Filter).ifGrassDrug(oeordId, oeordSub)
    					i ifGrassFlag = "Y"{
	    					s originOrderRid = oeordId
	    					s originOrderSid = oeordSub
	    					s childOrderIdStr = ##class(Nur.Z.NeedUnit.OrderSheet).GetGroupOrderIdStr(oeordId, oeordSub)
	    					s len = $l(childOrderIdStr,"^")
	    					s maxLen = 6
	    					s nowLen = 1
		    				f i=1:1:len{
			    				i i#maxLen=1{
				    				s orderId = $p(childOrderIdStr,"^",i)
				    				s oeordId = $p(orderId,"||",1)
				    				s oeordSub = $p(orderId,"||",2)
				    				d insertOrdList
				    			}
			    			}
			    			s oeordId = originOrderRid
			    			s oeordSub = originOrderSid
	    				}else{
		    				d insertOrdList
		    			}
```