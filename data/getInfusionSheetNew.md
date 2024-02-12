
```
/// w ##class(Nur.NIS.Service.OrderExcute.Print).getInfusionSheetNew()
ClassMethod getInfusionSheetNew(orderStr, sttNum = "", sheetCode = "")
{
	s ^Temp("getInfusionSheetNew")=$lb(orderStr, sttNum, sheetCode)
	q:orderStr="" ""
	s orderId=$p(orderStr,"^",1)
	s oeordId=$p(orderId,"||",1)
	s oeordSub=$p(orderId,"||",2)
	s oeoreSub=$p(orderId,"||",3)
	s EpisodeID=$p($g(^OEORD(oeordId)),"^",1)
	// 病人姓名
	s val("patName")=$p(^PAPER($p(^PAADM(EpisodeID),"^",1),"ALL"),"^",1)
	// 登记号
	s val("regNo")=##class(Nur.NIS.Service.Base.Patient).GetRegNo(EpisodeID)
	// 性别
	s val("sex")=$p($g(^CT("SEX",$p($g(^PAPER($p(^PAADM(EpisodeID),"^",1),"ALL")),"^",7))),"^",2)
	// 年龄
	s val("age")=##class(Nur.NIS.Service.Base.Patient).GetAge(EpisodeID)
	// 	床号
	s val("bedCode")=##class(Nur.NIS.Service.Base.Patient).GetBedCode(EpisodeID)
	//  科室
	s val("locdesc")=##class(Nur.NIS.Service.Base.Patient).GetCtLocDesc(EpisodeID)
	//  病案号
	s val("medicareNo")=##class(Nur.NIS.Service.Base.Patient).GetMedicareNo(EpisodeID)
	//  医嘱信息
	s len=$l(orderStr,"^")
	s:sttNum="" sttNum=1
	s printObj = ##class(ext.util.JsonObject).%New()
	for i=sttNum:1:(sttNum+len-1)  d
	.s orderId=$p(orderStr,"^",i-sttNum+1)
	.s oeordId=$p(orderId,"||",1)
	.s oeordSub=$p(orderId,"||",2)
	.s oeoreSub=$p(orderId,"||",3)
	.s sttdate=$zd($p($g(^OEORD(oeordId,"I",oeordSub,3)),"^",7),3)  //开医嘱日期
	.s stttime=$zt($p($g(^OEORD(oeordId,"I",oeordSub,1)),"^",17))   //开医嘱时间
	.//s sttdoctor=##class(Nur.OrderStaticInfo).getCtcpDesc(oeordId,oeordSub)   //开医嘱人
	.s sttdoctor=##class(Nur.OrderStaticInfo).getSeeOrderUserName(oeordId,oeordSub)   //处理护士
	.s endDateTime=##class(Nur.OrderStaticInfo).getXDateTime(oeordId,oeordSub) //停医嘱日期时间
	.s enddate=$p(endDateTime," ",1)  //停医嘱日期
	.s endtime=$p(endDateTime," ",2)  //停医嘱时间
	.s:endtime'="" endtime=$p(endtime,":",1)_":"_$p(endtime,":",2)  ////停医嘱时间-格式处理
	.s endDoctor=##class(Nur.OrderStaticInfo).getXCtcpDesc(oeordId,oeordSub)  //停医嘱人
	.s arcimDesc=##class(Nur.OrderStaticInfo).getArcimDesc(oeordId,oeordSub)  //医嘱名称
	.s notes=##class(Nur.OrderStaticInfo).getOrdNode(oeordId,oeordSub)   //备注
	.;s arcimDesc=arcimDesc_" "_notes
	.s PhcinDesc=##class(Nur.OrderStaticInfo).getPhcinDesc(oeordId,oeordSub) //用法
	.s PhcfrCode=##class(Nur.OrderStaticInfo).getPhcfrCode(oeordId,oeordSub) //频次
	.s FlowSpeed=##class(Nur.OrderStaticInfo).getFlowSpeed(oeordId,oeordSub)  //滴速
	.s doseQtyUnit=##class(Nur.OrderStaticInfo).GetdoseQtyUnit(oeordId,oeordSub) //剂量
	.s OEORIItmMastDR=$p(^OEORD(oeordId,"I",oeordSub,1),"^",2)   //取医嘱大类ID
 	.s s1=$p(OEORIItmMastDR,"||",1)
 	.s s2=$P(OEORIItmMastDR,"||",2)
 	.s ARCIMItemCatDR=$p(^ARCIM(s1,s2,1),"^",10)
 	.s ARCICExecCategDR=$p(^ARC("IC",ARCIMItemCatDR),"^",8)        ///
	.;s arcimDesc=arcimDesc_" "_doseQtyUnit_" "_PhcfrCode_" "_PhcinDesc
	.i sheetCode'="LSZXDDD"  d
	..s arcimDesc=arcimDesc_" "_doseQtyUnit_" "_notes
#;	..i ((ARCICExecCategDR="2")||(ARCICExecCategDR="3")||(ARCICExecCategDR="4")||(ARCICExecCategDR="5")||(ARCICExecCategDR="6")||(ARCICExecCategDR="7")||(ARCICExecCategDR="8")||(ARCICExecCategDR="9")||(ARCICExecCategDR="16"))
#; 	...s arcimDesc=arcimDesc_" 剂量:"_doseQtyUnit_" "_notes
	..s child=0
	..f  s child=$O(^OEORDi(0,"OEORI",oeordId,oeordId_"||"_oeordSub,child)) q:child=""  d
	...s orcatDesc=##class(Nur.OrderStaticInfo).getOrcatDesc(oeordId,child) //医嘱大类
	...q:(orcatDesc["材料")&&(sheetCode="CQSYD")  //输液单过滤绑定的材料医嘱
	...s arcimDescSub="\n______"_##class(Nur.OrderStaticInfo).getArcimDesc(oeordId,child) //子医嘱名称
	...s notesSub=##class(Nur.OrderStaticInfo).getOrdNode(oeordId,child)  //备注
	...s doseQtyUnitSub=##class(Nur.OrderStaticInfo).GetdoseQtyUnit(oeordId,child) //剂量
	...s arcimDescSub=arcimDescSub_" "_notesSub_" "_doseQtyUnitSub
#;	...i ((ARCICExecCategDR="2")||(ARCICExecCategDR="3")&&(ARCICExecCategDR="4")||(ARCICExecCategDR="5")&&(ARCICExecCategDR="6")||(ARCICExecCategDR="7")||(ARCICExecCategDR="8")||(ARCICExecCategDR="9")||(ARCICExecCategDR="16"))
#; 	....s arcimDescSub=arcimDescSub_" "_notesSub_" 剂量:"_doseQtyUnitSub
	...s arcimDesc=arcimDesc_arcimDescSub
	.e  d
	..s arcimDesc=arcimDesc_" "_doseQtyUnit_" "_PhcfrCode_" "_PhcinDesc_" "_FlowSpeed_" "_notes
#;	..i ((ARCICExecCategDR="2")||(ARCICExecCategDR="3")||(ARCICExecCategDR="4")||(ARCICExecCategDR="5")||(ARCICExecCategDR="6")||(ARCICExecCategDR="7")||(ARCICExecCategDR="8")||(ARCICExecCategDR="9")||(ARCICExecCategDR="16"))
#; 	...s arcimDesc=arcimDesc_" 剂量:"_doseQtyUnit_" "_PhcfrCode_" "_PhcinDesc_" "_FlowSpeed_" "_notes
	..s:$p($g(^OEORD(oeordId,"I",oeordSub,11)),"^",39)'="" arcimDesc="______"_arcimDesc
	.i arcimDesc'="" s arcimDesc=##class(Nur.NIS.Common.StringUtils).getSplitStr(arcimDesc,"13")
	.d printObj.Put("credate"_i,sttdate)
	.d printObj.Put("cretime"_i,$p(stttime,":",1)_":"_$p(stttime,":",2))
	.d printObj.Put("signName"_i,sttdoctor)
	.d printObj.Put("arcimDesc"_i,arcimDesc_"\n")
	.d printObj.Put("enddate"_i,enddate)
	.d printObj.Put("endtime"_i,endtime)
	.d printObj.Put("enddoctor"_i,endDoctor)
	.d printObj.Put("phcinDesc"_i,PhcinDesc)
	.d printObj.Put("phcfrCode"_i,PhcfrCode)
	.d printObj.Put("flowSpeed"_i,FlowSpeed)
	.;d printObj.Put("doseQtyUnit"_i,doseQtyUnit)
	
	s val("sheetName")=""
	s:sheetCode="CQSYD" val("sheetName")= "  长期医嘱执行单（输液类）"
	s:sheetCode="CUDY" val("sheetName")=  "      出院带药执行单"
	s:sheetCode="HLZLD" val("sheetName")= "      处置治疗执行单"
	s:sheetCode="JCD" val("sheetName")=   "      检查医嘱执行单"
	s:sheetCode="SYD" val("sheetName")=   "  临时医嘱执行单（输液类）"
	s:sheetCode="ZSD" val("sheetName")=   "  临时医嘱执行单（注射类）"
	s:sheetCode="CQZSD" val("sheetName")= "  长期医嘱执行单（注射类）"
	s:sheetCode="cqkfyd" val("sheetName")=" 长期医嘱执行单（口服药类）"
	s:sheetCode["LSKFYD" val("sheetName")=" 临时医嘱执行单（口服药类）"
	s:sheetCode="WHD" val("sheetName")=   "      雾化医嘱执行单"
	s:sheetCode="PSD" val("sheetName")=   "      皮试医嘱执行单"
	s:sheetCode="SXD" val("sheetName")=   "      输血医嘱执行单"
	s:sheetCode="SQZBD" val("sheetName")=   "      术前准备执行单"
	s:sheetCode="CUDY" val("sheetName")=   "      出院带药执行单"
	s:sheetCode="SSZXD" val("sheetName")=   "      手术医嘱执行单"
	
	
	
	do printObj.Put("hospitalName","邛崃市医疗中心医院")
	do printObj.Put("sheetName",val("sheetName"))
	do printObj.Put("patName",val("patName"))
	do printObj.Put("regNo",val("regNo"))
	do printObj.Put("bedCode",val("bedCode"))
	do printObj.Put("sex",val("sex"))
	do printObj.Put("age",val("age"))
	do printObj.Put("locdesc",val("locdesc"))
	do printObj.Put("medicareNo",val("medicareNo"))
	do printObj.Put("crrYear",+$zd(+$h,3)_"年")
	set rtn = printObj.GetPrintData()
	do printObj.%Close()
	Set printObj = ""
	quit rtn
}
```