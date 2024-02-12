```
/// 给第三方调用，保存呼叫信息
/// ##class(Nur.Custom.UnitNeed.UnitNeed20230811).OtherSaveOpPatCall("")
ClassMethod OtherSaveOpPatCall(XmlParr)
{
	s result = 0
	//
	s XmlParr ="<Request><LISRefundInfoList><LISRefundInfo><OrdID>1197967||4</OrdID><PhexamId>JY15019</PhexamId><Table_Id>1199570</Table_Id><UserCode>004</UserCode><RegStatus>Y</RegStatus></LISRefundInfo><LISRefundInfo><OrdID>1197967||3</OrdID><PhexamId>JY01030</PhexamId><Table_Id>1199570</Table_Id><UserCode>004</UserCode><RegStatus>Y</RegStatus></LISRefundInfo><LISRefundInfo><OrdID></OrdID><PhexamId>现场挂号</PhexamId><Table_Id>1199570</Table_Id><UserCode>004</UserCode><RegStatus>Y</RegStatus></LISRefundInfo></LISRefundInfoList></Request>"

	s currDateL = $p($h,",",1)

	s xmlObj=##class(PHA.COM.XML).Parse(XmlParr)
	s list=xmlObj.LISRefundInfoList
	s index=""
	for
	{
		s index=$o(list.%xmlNodeValue(index))
		q:index=""
		s LISRefundInfo=list.%xmlNodeValue(index)
		s obj = ##class(Nur.Custom.Entity.OpPatCall).%New()
		s obj.TableId = LISRefundInfo.Get("Table_Id")
		s obj.OrdID = LISRefundInfo.OrdID
		s obj.CallDate = currDateL
		s obj.UserCode = LISRefundInfo.Get("UserCode")
		s obj.RegStatus = LISRefundInfo.Get("RegStatus")
		s obj.PhexamId = LISRefundInfo.Get("PhexamId")
		s sc = obj.%Save()
		i $$$ISERR(sc) s result=$SYSTEM.Status.GetErrorText(sc)

	}

	q result
}
```