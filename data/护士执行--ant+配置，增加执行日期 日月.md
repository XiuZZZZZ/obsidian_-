日/月

```
//Nur.NIS.Service.OrderExcute.OrderInfo
ClassMethod GetNeedExcuteDateMD(orderId)
{
	n (orderId,%session)
	s rid = $p(orderId,"||",1)
	s sid = $p(orderId,"||",2)
	s eid = $p(orderId,"||",3)
	s needDateL = $p(^OEORD(rid,"I",sid,"X",eid),"^",1)
	s needTimeL = $p(^OEORD(rid,"I",sid,"X",eid),"^",2)
	s dayH = $p($zd(needDateL,3),"-",3)
	s monthH = $p($zd(needDateL,3),"-",2)
	q dayH_"/"_monthH
}
```