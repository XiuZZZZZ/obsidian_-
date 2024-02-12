

- 过程
	- 数据
		- 医嘱：63183||20
		- 方法：Nur.NIS.Service.OrderExcute.OrderInfo  GetPhOrdQtyUnit
	- 方法有问题
	- 更新成医生站取总量方法  [[#医生站取总量方法]]




## 医生站取总量方法

```
ClassMethod GetPhOrdQtyUnit(oeoriId As %String) As %String
{
	n (oeoriId, %session)
	s oeordId = $p(oeoriId,"||",1)
	s oeoriSub = $p(oeoriId,"||",2)
	s SumQty=""
	s str1=^OEORD(oeordId,"I",oeoriSub,1)
	s str2 = $g(^OEORD(oeordId,"I",oeoriSub,2))
	s PriorityDR = $p(str1,"^",8)
	s ItmMastDR = $p(str1,"^",2)
	s doseUnitDr= $p(str2,"^",3)	;剂量单位 OEORI_Unit_DR
	s ItemCatDR=$p(^ARCIM(+ItmMastDR,$p(ItmMastDR,"||",2),1),"^",10)
	s OrderType=$P(^ARC("IC",ItemCatDR),"^",7)
	s doseUOM=##class(web.DHCDocOrderCommon).GetUOMDesc(doseUnitDr)
	
    i ##class(appcom.OEOrdItem).ISShortOrderPrior(PriorityDR,"") { //,OrderType="R"
	    s BillUOMRowid=$p($g(^ARCIM(+ItmMastDR,$p(ItmMastDR,"||",2),8)),"^",14),BillUOMDesc=##class(web.DHCDocOrderCommon).GetUOMDesc(BillUOMRowid)
	    s CheckCHNFlag=##class(web.DHCSTINTERFACE).GetStruModeFlag(ItmMastDR)
		if (CheckCHNFlag="Y") {
		    s Phcdf=$P($g(^ARCIM(+ItmMastDR,$P(ItmMastDR,"||",2),1)),"^",12)
		    if Phcdf'="" s BillUOMRowid=$p(^PHCD(+Phcdf,"DF",$p(Phcdf,"||",2),2),"^",4),BillUOMDesc=##class(web.DHCDocOrderCommon).GetUOMDesc(BillUOMRowid),TBillUom=BillUOMDesc
		}
	    s phqtyord=$p($g(^OEORD(oeordId,"I",oeoriSub,1)),"^",12)
	    s phqtyord=##class(web.DHCDocOrderView).formateNum(phqtyord)
	    s PackQty=$p($g(^OEORD(oeordId,"I",oeoriSub,9)),"^",4)
		s PackQty=##class(web.DHCDocOrderView).formateNum(PackQty)
		;协议单位
		s ProtocolPackUOMDR=$p($g(^OEORD(oeordId,"I",oeoriSub,"DHC")),"^",13)
		s:ProtocolPackUOMDR'="" ProtocolPackUOM=##class(web.DHCDocOrderCommon).GetUOMDesc(ProtocolPackUOMDR)
		i OrderType="R" {
			/*if PackQty'=""{
				i ProtocolPackUOMDR'="" s PackQty=PackQty_"<font style='font-style:italic;text-decoration:underline'>"_ProtocolPackUOM_"</font>",SumQty=PackQty
				e  s PackQty=PackQty_"<font style='font-style:italic;text-decoration:underline'>"_BillUOMDesc_"</font>",SumQty=PackQty
			}else {
				i phqtyord'="" {
					s SumQty=phqtyord_"<font style='font-style:italic;text-decoration:underline'>"_doseUOM_"</font>"
				}
			}*/
			s OrdPackQtyInfo=##Class(web.DHCDocQryOEOrder).GetOrdPackQtyInfo(oeordId_"||"_oeoriSub)
			if PackQty'=""{
				s SumQty=##class(DHCDoc.Util.Base).FormateNumber(+$p(OrdPackQtyInfo,"^",1))_" "_$p(OrdPackQtyInfo,"^",2)_""
			}else{
				s SumQty=##class(DHCDoc.Util.Base).FormateNumber(+$p(OrdPackQtyInfo,"^",4))_" "_$p(OrdPackQtyInfo,"^",5)_""
			}
		}else {
			i +phqtyord'=0 s SumQty=phqtyord_" "_BillUOMDesc_""
		}
    }
	
	q SumQty
}
```



