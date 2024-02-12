```js
/// 获得医嘱大类Id
/// ##class(Nur.Custom.Dll).GetOrderCategoryId()
ClassMethod GetOrderCategoryId(OrderRid, OrderSid)
{
	s arcimId = $p(^OEORD(OrderRid,"I",OrderSid,"1"),"^",2)
	s arcimCatId = $p(^ARCIM(+arcimId ,$p(arcimId ,"||",2),1),"^",10)
	//子类信息
	s arcimCatData = ^ARC("IC",arcimCatId)
	//w arcimCatData,!
	s arcimCategoryId = $p(arcimCatData,"^",8)
	//大类信息
	s arcimCategoryData = ^OEC("ORCAT",arcimCategoryId)
	s orderCategoryCode = $p(arcimCategoryData,"^",1)
	s orderCategoryDesc = $p(arcimCategoryData,"^",2)
	
	q orderCategoryDesc
}

```
