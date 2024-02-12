```
/// w ##class(Nur.NIS.Service.Base.Transfer).getIfCanTrans(11)
ClassMethod getIfCanTrans(userId)
{
	s careProcTpDesc = ""
	s ^temp("getIfCanTrans")=$lb(userId)
	i '$d(^SSU("SSUSR",userId)) q careProcTpDesc
	s careProvId = $p(^SSU("SSUSR",userId),"^",14)
	i careProvId="" q careProcTpDesc
	s careProcTpId = $p(^CTPCP(careProvId,1),"^",4)

	s careProcTpDesc = $p(^CT("CPT",careProcTpId),"^",2)
	q careProcTpDesc
}

```