

```
/// 获取传进来的医嘱对应的主医嘱有多个 并且按照不同的病人分开  只取长期医嘱 
/// zhangxiangbo
/// 20181225
/// w ##class(Nur.NIS.Service.OrderExcute.Print).getOrdNum("970806||46||1^970806||47||1^970806||48||1^970806||4||1^970806||5||1","DHCNURMouldPrnCQSYZXD")
ClassMethod getOrdNum(orderIdStr As %String) As %String
{
	s ^TempZXB("45453")=orderIdStr
	s ArcItemStr=""
	s len=$L(orderIdStr,"^")
	s CurPat="",RealOrder=""
	for i=1:1:len d
	.s CurOrd=$P(orderIdStr,"^",i)
	.q:(CurOrd="")
	.i CurPat="" s CurPat=+CurOrd
	.s oecprID=$p($g(^OEORD(+CurOrd,"I",$P(CurOrd,"||",2),1)),"^",8)
	.s priorDesc=""
	.s:oecprID'="" priorDesc=$P(^OECPR(oecprID),"^",2) 
	.;q:(priorDesc'["长期")
	.s main=$p($g(^OEORD(+CurOrd,"I",$P(CurOrd,"||",2),11)),"^",39)
	.s arcimID=$p($g(^OEORD(+CurOrd,"I",$P(CurOrd,"||",2),1)),"^",2)
	.q:((ArcItemStr'="")&&(ArcItemStr'[("^"_arcimID_"^")))
	.i main="" d
	..i ("^"_RealOrder_"^")'[("^"_+CurOrd_"||"_$P(CurOrd,"||",2)_"^") d
	...i RealOrder="" s RealOrder=(+CurOrd_"||"_$P(CurOrd,"||",2))
	...e  d
	....i CurPat'=+CurOrd s RealOrder=RealOrder_"@"_(+CurOrd_"||"_$P(CurOrd,"||",2)),CurPat=+CurOrd
	....e  s RealOrder=RealOrder_"^"_(+CurOrd_"||"_$P(CurOrd,"||",2))
	.e  d
	..i ("^"_RealOrder_"^")'[("^"_main_"^") d
	...i RealOrder="" s RealOrder=main
	...e  d
	....i CurPat'=+CurOrd s RealOrder=RealOrder_"@"_main,CurPat=+CurOrd
	....e  s RealOrder=RealOrder_"^"_main 
	q RealOrder
}
```