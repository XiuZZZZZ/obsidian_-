```
/// Creator: 		pengjunfu
/// Description: 	根据query获取elementUI table对应的column数据
/// CreatDate: 		2018.10.31
/// Other           w ##class(Nur.QueryBrokerNew).getTableColumnOfHISUI("Nur.CommonInterface.Temperature","findEvent")
/// Return：
ClassMethod getTableColumnOfHISUI(className As %String, classQuery As %String) As %String
{
	s rs=##class(%Library.ResultSet).%New(className_":"_classQuery)
	s columnNum=rs.GetColumnCount()
	s ret="["
	f i=1:1:columnNum
	{
		s columnHeader=rs.GetColumnHeader(i)
		s columnName=rs.GetColumnName(i)
		s json=##class(%ArrayOfDataTypes).%New()
		d json.SetAt(columnName,"field")
		d json.SetAt(columnHeader,"title")
		i columnHeader="hidden" s width=0
		e  s width=120
		d json.SetAt(width,"width")
		s ret=ret_##class(Nur.JSON).Encode(json)
		i i'=columnNum s ret=ret_","

	}
	s ret=ret_"]"
	q ret
}

```