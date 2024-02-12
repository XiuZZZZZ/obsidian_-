
```
w ##class(Nur.NIS.Service.Base.Patient).GetCareLevel(176400)  
二级护理
```

# 实现

```
/// Creator: 		pengjunfu
/// CreatDate: 		2017.5.14
/// Description: 	获取病人护理级别描述
/// Input：			EpisodeID: 就诊号
/// Return：		
/// Other           w ##class(Nur.NIS.Service.Base.Patient).GetCareLevel("12")
ClassMethod GetCareLevel(EpisodeID)
{
	n (EpisodeID,%session)
	s ArcimDesc="特级护理"
	s arcimCodes=##class(Nur.NIS.Service.Base.DataRelationConfig).GetArcCode("CRITICALCARE")
	d ProcessCodes
	s ArcimDesc="一级护理"
	s arcimCodes=##class(Nur.NIS.Service.Base.DataRelationConfig).GetArcCode("FIRSTCLSCARE")
	d ProcessCodes
	s ArcimDesc="二级护理"
	s arcimCodes=##class(Nur.NIS.Service.Base.DataRelationConfig).GetArcCode("SECONDCLSCARE")
	d ProcessCodes
	s ArcimDesc="三级护理"
	s arcimCodes=##class(Nur.NIS.Service.Base.DataRelationConfig).GetArcCode("THIRDCLSCARE")
	d ProcessCodes
	s r1=$o(careSet(""))
	q:r1="" ""
	s r2=$o(careSet(r1,""),-1)
	q:r2="" ""
	s r3=$o(careSet(r1,r2,""),-1)
	q careSet(r1,r2,r3)
ProcessCodes
	s num=$l(arcimCodes,"^")
 	for i=1:1:num d
 	.s code=$p(arcimCodes,"^",i)
 	.q:code=""
 	.s rowidm=$o(^ARCIM(0,"Code",$$ALPHAUP^SSUTIL4(code),""))
 	.q:rowidm=""
 	.s ArcimID=rowidm_"||"_"1"
 	.d GetCareTypeByOrd
	q
GetCareTypeByOrd
	#;100 无此护理级别
	#;1 长期
	#;2 临时
	s ret=100
	s oeord=$o(^OEORD(0,"Adm",EpisodeID,""))
	q:oeord="" ret
	s OEORISttDat="" 
	f  s OEORISttDat=$o(^OEORDi(0,"ARCIM",+oeord,ArcimID,OEORISttDat),-1) q:(OEORISttDat="")!(ret=1)  d
	.q:OEORISttDat>+$h
	.s oeordSub="" f  s oeordSub=$o(^OEORDi(0,"ARCIM",+oeord,ArcimID,OEORISttDat,oeordSub),-1) q:(oeordSub="")!(ret=1)  d
	..s ordStatId=$p(^OEORD(+oeord,"I",oeordSub,1),"^",13)
	..s ordStat=$p($g(^OEC("OSTAT",ordStatId)),"^",1)
	..q:ordStat'="V"
	..s sttTim=$p(^OEORD(+oeord,"I",oeordSub,1),"^",10)
	..s priorityID=$p(^OEORD(+oeord,"I",oeordSub,1),"^",8)
	..s priorityDesc=$p(^OECPR(priorityID),"^",2)
	..i priorityDesc["长期" s ret=1
	..e  s ret=2
	..s careSet(ret,OEORISttDat,sttTim)=ArcimDesc
	q
}
```