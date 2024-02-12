```
/// 指定日期范围内，宝贝计划的婴儿数据
/// d ##class(%ResultSet).RunQuery("Nur.Data.UnitMethod","QueryBabyPlan","2017-06-28")
Query QueryBabyPlan(StartDate,EndDate) As websys.Query(ROWSPEC = "queName:%String:护理问题,queName:%String:护理问题")
{
}

ClassMethod QueryBabyPlanExecute(ByRef qHandle As %Binary, StartDate,EndDate) As %Status
{
	set repid=$I(^CacheTemp)
 	s ind=1
 	s qHandle=$lb(0,repid,0)
	//以下是实例代码
	s ^temp("getDrugDetail2") = $lb(StartDate,EndDate)
	f i=1:1:2{
		s doctor="医生"_i
		d OutQueryBabyPlan

	}
	Set qHandle=$lb(0,repid,0)
	Quit $$$OK
OutQueryBabyPlan
	set Data=$lb(doctor)
 	Set ^CacheTemp(repid,ind)=Data
 	Set ind=ind+1
	quit
}


```