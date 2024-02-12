```
Class web.DHCNurSignVerify Extends (%RegisteredObject, %XML.Adaptor) [ Inheritance = right, Not ProcedureBlock ]
{

// w ##class(web.DHCNurSignVerify).GetUserSignImage("2532")

ClassMethod GetUserSignImage(usrID As %String) As %String
{
 s id = ##Class(CA.UsrSignatureInfo).GetInUseID(usrID)
 q:""=id ""
 s info=##Class(CA.UsrSignatureInfo).%OpenId(id) 
 q info.SignImage
}

ClassMethod GetUserDesc(usrID As %String) As %String
{
 q:usrID="" ""
 s ret=""
 s ret=$p($g(^SSU("SSUSR",usrID)),"^",2)
 q ret
}

/// creator:      panshuai
/// Date:         2012-07-13
/// Description:  获取执行信息主要信息
/// w ##class(web.DHCNurSignVerify).GetOEOREItemXML("1299667||214||312","已执行^^2023-11-03^11:31^12546^WZX","1")
ClassMethod GetOEOREItemXML(OeoreId As %String, ExecInfo As %String, OperationType As %String = "") As %String
{
	n (OeoreId,ExecInfo,OperationType)
	s ^temp("GetOEOREItemXML")=$lb(OeoreId,ExecInfo,OperationType)
	q:OeoreId="" ""
	
	s OeordId=$P(OeoreId,"||",1)
	s OeoriSub=$P(OeoreId,"||",2)
	s OeoreSub=$P(OeoreId,"||",3)
	
	s ExecStatDesc=$p(ExecInfo,"^",1) ;执行状态
	s ExecNotes=$p(ExecInfo,"^",2) ;皮试批号
	s ExecDat=$p(ExecInfo,"^",3) ;执行日期
	s:ExecDat'="" ExecDat=##Class(websys.Conversions).DateLogicalToHtml(ExecDat)
	i ExecDat="" s ExecDat=+$h
	
	s ExecTim=$p(ExecInfo,"^",4) ;执行时间
	i ExecTim="" s ExecTim=$p($h,",",2)
	s ExecUser=$p(ExecInfo,"^",5) ;执行人
	s ExecType=$p(ExecInfo,"^",6) ;执行单据类型
	
	s ordstr1=$g(^OEORD(OeordId,"I",OeoriSub,1))
	s ordstr2=$g(^OEORD(OeordId,"I",OeoriSub,2))
	s ordstr3=$g(^OEORD(OeordId,"I",OeoriSub,3))
	s ordstr5=$g(^OEORD(OeordId,"I",OeoriSub,5))
	s ordstr7=$g(^OEORD(OeordId,"I",OeoriSub,7))
	s ordstr9=$g(^OEORD(OeordId,"I",OeoriSub,9))
	s ordstr11=$g(^OEORD(OeordId,"I",OeoriSub,11))
	
	;主要信息
	s Priority=$p(ordstr1,"^",8)	     ;医嘱优先级
	s:Priority'="" Priority=$p($G(^OECPR(Priority)),"^",2)
	s ArcimId=$p(ordstr1,"^",2)		     ;医嘱名称
	s ArcimId1=$p(ArcimId,"||",1),ArcimId2=$p(ArcimId,"||",2)
	s ArcimDesc=$p($G(^ARCIM(ArcimId1,ArcimId2,1)),"^",2)
	s DoseQty=$p(ordstr2,"^",1)		     ;单次剂量
	s DoseUnit=$p(ordstr2,"^",3)	     ;单次剂量单位
	s:DoseUnit'="" DoseUnit=$p(^CT("UOM",DoseUnit),"^",2)
	s Freq=$p(ordstr2,"^",4)	         ;频次
	s:Freq'="" Freq=$P($G(^PHCFR(Freq)),"^",2)
	s Instr=$p(ordstr2,"^",7)			 ;用法
	s:Instr'="" Instr=$P($G(^PHCIN(Instr)),"^",2)
	
	s PrescNo=$p(ordstr1,"^",14)	     ;处方号
	s OrdStatus=$p(ordstr1,"^",13)       ;医嘱状态
	s:$g(OrdStatus)'="" OrdStatus=$p(^OEC("OSTAT",OrdStatus),"^",2)
	s UserDepartment=$p(ordstr7,"^",2)	 ;开单科室
	s:UserDepartment'="" UserDepartment=$p(^CTLOC(UserDepartment),"^",2)
	s RecDep=$p(ordstr3,"^",6)			 ;接收科室
	s:RecDep'="" RecDep=$p(^CTLOC(RecDep),"^",2)
	s Doctor=$p(ordstr1,"^",11)		     ;下医嘱医生
	s:Doctor'="" Doctor=$p($g(^CTPCP(Doctor,1)),"^",1)
	;备注
	f rnum=1:1:$g(^OEORD(OeordId,"I",OeoriSub,"DEP",0))  d
	.s Notes=$g(Notes)_$g(^OEORD(OeordId,"I",OeoriSub,"DEP",rnum))
	
	s PhQtyOrd=$p(ordstr1,"^",12)	 ;总量
	s BillUOM=$p($g(^ARCIM(ArcimId1,ArcimId2,8)),"^",14) ; 整包娤单位
	s:BillUOM'="" BillUOM=$p($g(^CT("UOM",BillUOM)),"^",2)
	
	s Dura=$p(ordstr2,"^",6)			 ;疗程
	s:Dura'="" Dura=$p($G(^PHCDU(Dura)),"^",2)
	s LabEpisodeNo=$p(ordstr3,"^",20) 	;标本号
	s OrdSkinTest=$p(ordstr5,"^",2)	  	;皮试

	s UseAdd=$p(ordstr7,"^",1)		;下医嘱用户
	s:UseAdd'="" UseAdd=$p($g(^SSU("SSUSR",UseAdd)),"^",1)
	s OrdCreateDate=$p(ordstr3,"^",7)    ;下医嘱日期
	s:OrdCreateDate'="" OrdCreateDate=$zd(OrdCreateDate,3)
	s OrdCreateTime=$p(ordstr1,"^",17)   ;下医嘱时间
	s:OrdCreateTime'="" OrdCreateTime=$zt(OrdCreateTime,2)
	
	s StopDoctor=$p(ordstr3,"^",29)		;停医嘱人
	s:StopDoctor'="" StopDoctor=$p($g(^CTPCP(StopDoctor,1)),"^",1)
	s StopDate=$p(ordstr3,"^",34) 		;停医嘱日期
	s:StopDate'="" StopDate=$zd(StopDate,3)
	s StopTime=$p(ordstr2,"^",15) 		;停医嘱时间
	s:StopTime'="" StopTime=$zt(StopTime,2)
	
	s OrdStartDate=$p(ordstr1,"^",9)     ;医嘱开始日期
	s:OrdStartDate'="" OrdStartDate=$zd(OrdStartDate,3)
	s OrdStartTime=$p(ordstr1,"^",10)    ;医嘱开始时间
	s:OrdStartTime'="" OrdStartTime=$zt(OrdStartTime,2)
	
	i OeoreSub'="" d 
	.s oreStr=^OEORD(OeordId,"I",OeoriSub,"X",OeoreSub)
	.s OrdStartDate=$p(oreStr,"^",1)
	.i OrdStartDate'="" s OrdStartDate=$zd(OrdStartDate,3)
	.s OrdStartTime=$p(oreStr,"^",2)
	.i OrdStartTime'="" s OrdStartTime=$zt(OrdStartTime,2)
	
	s OEOREMainXML=""
	s OeoreItemStr=Priority_"^"_ArcimDesc_"^"_DoseQty_"^"_DoseUnit_"^"_Freq_"^"_Instr
	s OeoreItemStr=OeoreItemStr_"^"_PrescNo_"^"_OrdStatus_"^"_UserDepartment_"^"_RecDep_"^"_Doctor_"^"_$g(Notes)
	s OeoreItemStr=OeoreItemStr_"^"_PhQtyOrd_"^"_BillUOM_"^"_Dura_"^"_LabEpisodeNo_"^"_OrdSkinTest_"^"_UseAdd
	s OeoreItemStr=OeoreItemStr_"^"_OrdCreateDate_"^"_OrdCreateTime_"^"_StopDoctor_"^"_StopDate_"^"_StopTime
	s OeoreItemStr=OeoreItemStr_"^"_OrdStartDate_"^"_OrdStartTime
	s OeoreItemStr=OeoreItemStr_"^"_ExecStatDesc_"^"_ExecNotes_"^"_ExecDat_"^"_ExecTim_"^"_ExecUser_"^"_ExecType
	
	d ..GetOEOREMainXML(OeoreItemStr,OperationType,.OEOREMainXML)
	q OEOREMainXML
}

/// creator:      panshuai
/// Date:         2012-07-13
/// Description:  执行信息主要信息转换XML格式
/// w ##class(web.DHCNurSignVerify).GetOEOREMainXML()
ClassMethod GetOEOREMainXML(OrdItemStr As %String, OperationType As %String = "", ByRef OEOREMainXML As %String = "") As %String
{
	n (OrdItemStr,OperationType,OEOREMainXML)
	i OperationType=""  s OperationType=1
	i OperationType=1  
	{
	s Object=##class(web.DHCNurEntityExecInfo).%New()
	s Object.Priority=$p(OrdItemStr,"^",1)
	s Object.ArcimDesc=$p(OrdItemStr,"^",2)
	s Object.DoseQty=$p(OrdItemStr,"^",3)
	s Object.DoseUnit=$p(OrdItemStr,"^",4)
	s Object.Freq=$p(OrdItemStr,"^",5)
	s Object.Instr=$p(OrdItemStr,"^",6)
	s Object.PrescNo=$p(OrdItemStr,"^",7)
	s Object.OrdStatus=$p(OrdItemStr,"^",8)
	s Object.UserDepartment=$p(OrdItemStr,"^",9)
	s Object.RecDep=$p(OrdItemStr,"^",10)
	s Object.Doctor=$p(OrdItemStr,"^",11)
	s Object.Notes=$p(OrdItemStr,"^",12)
	s Object.PhQtyOrd=$p(OrdItemStr,"^",13)
	s Object.BillUOM=$p(OrdItemStr,"^",14)
	s Object.Dura=$p(OrdItemStr,"^",15)
	s Object.LabEpisodeNo=$p(OrdItemStr,"^",16)
	s Object.OrdSkinTest=$p(OrdItemStr,"^",17)
	s Object.UseAdd=$p(OrdItemStr,"^",18)
	s Object.OrdCreateDate=$p(OrdItemStr,"^",19)
	s Object.OrdCreateTime=$p(OrdItemStr,"^",20)
	s Object.StopDoctor=$p(OrdItemStr,"^",21)
	s Object.StopDate=$p(OrdItemStr,"^",22)
	s Object.StopTime=$p(OrdItemStr,"^",23)
	s Object.OrdStartDate=$p(OrdItemStr,"^",24)
	s Object.OrdStartTime=$p(OrdItemStr,"^",25)
	s Object.ExecStatDesc=$p(OrdItemStr,"^",26)
	s Object.ExecNotes=$p(OrdItemStr,"^",27)
	s Object.ExecDat=$p(OrdItemStr,"^",28)
	s Object.ExecTim=$p(OrdItemStr,"^",29)
	s Object.ExecUser=$p(OrdItemStr,"^",30)
	s Object.ExecType=$p(OrdItemStr,"^",31)
	d Object.XMLExportToString(.OEOREMainXML)	
	}
	Q OEOREMainXML
}

/// creator:      panshuai
/// Date:         2012-07-13
/// Description:  护理病历原文主要信息转换XML格式
/// w ##class(web.DHCNurSignVerify).GetRecMainXML()
ClassMethod GetRecMainXML(RecInfoStr As %String) As %String
{
	n (RecInfoStr)
	
	set tmp=""
    set aa=##class(web.DHCMGNurComm).setmoudtmp(RecInfoStr,.tmp)
    
	s RecMainXML=""
	
	s Object=##class(web.DHCNurEntityRecInfo).%New()
	f i=1:1:100 
	{
		s itm="Item"_i  
		if $g(tmp(itm))'="" s $ZOBJPROPERTY(Object,itm)=$g(tmp(itm))
	}
	if $g(tmp("CareDate"))'="" s Object.CareDate=$g(tmp("CareDate"))
	if $g(tmp("CareTime"))'="" s Object.CareTime=$g(tmp("CareTime"))
	if $g(tmp("DiagnosDr"))'="" s Object.DiagnosDr=$g(tmp("DiagnosDr"))
	if $g(tmp("CaseMeasure"))'="" s Object.CaseMeasure=$g(tmp("CaseMeasure"))
	d Object.XMLExportToString(.RecMainXML)	
	
	q RecMainXML
}

/// creator:      panshuai
/// Date:         2012-07-13
/// Description:  护理病历原文主要信息转换XML格式
/// w ##class(web.DHCNurSignVerify).GetTableRecMainXML()
ClassMethod GetTableRecMainXML(RecInfoStr As %String) As %String
{
	n (RecInfoStr)
	
    s l=$L(RecInfoStr,"^")
    for i=1:1:l
	{
	  s itm=$P(RecInfoStr,"^",i)
	  if itm="" continue
	  s name=$P(itm,"|")
	  s val=$P(itm,"|",2)
	  s tmp(name)=val
	}
	s RecMainXML=""
	
	s Object=##class(web.DHCNurEntityRecInfo).%New()
	f i=1:1:100 
	{
		s itm="Item"_i  
		if $g(tmp(itm))'="" s $ZOBJPROPERTY(Object,itm)=$g(tmp(itm))
	}
	if $g(tmp("CareDate"))'="" s Object.CareDate=$g(tmp("CareDate"))
	if $g(tmp("CareTime"))'="" s Object.CareTime=$g(tmp("CareTime"))
	if $g(tmp("DiagnosDr"))'="" s Object.DiagnosDr=$g(tmp("DiagnosDr"))
	if $g(tmp("CaseMeasure"))'="" s Object.CaseMeasure=$g(tmp("CaseMeasure"))
	d Object.XMLExportToString(.RecMainXML)	
	
	q RecMainXML
}

/// creator:      panshuai
/// Date:         2012-07-13
/// Description:  插入批量签名记录-执行医嘱
/// w ##class(web.DHCNurSignVerify).InsertBatchSignRecord()
ClassMethod InsertBatchSignRecord(CurrOrderItemStr As %String, UserID As %String, OperationType As %String = "", OrdItemsHashVal As %String, MainSignCert As %String, MainSignValue As %String, ExpStr As %String = "")
{
    s ^temp("InsertBatchSignRecord")=$lb(CurrOrderItemStr , UserID , OperationType  , OrdItemsHashVal , MainSignCert , MainSignValue , ExpStr )
	s ^TMP("InsertBatchSignRecord",1)=CurrOrderItemStr
	s ^TMP("InsertBatchSignRecord",2)=UserID
	s ^TMP("InsertBatchSignRecord",3)=OperationType
	s ^TMP("InsertBatchSignRecord",4)=OrdItemsHashVal
	s ^TMP("InsertBatchSignRecord",5)=MainSignCert
	s ^TMP("InsertBatchSignRecord",6)=MainSignValue
	s ret=0
	//s ^okki2(CurrOrderItemStr,OrdItemsHashVal,MainSignValue)=UserID
	f i=1:1:$l(CurrOrderItemStr,"^") q:ret'=0  d
	.s OrderId=$p(CurrOrderItemStr,"^",i)
	.q:(OrderId="")
	.s OEORIItemsHashVal=$p(OrdItemsHashVal,"&&&&&&&&&&",i)
	.s OrderMainSignValue=$p(MainSignValue,"&&&&&&&&&&",i)
	.//s ^okki(i,CurrOrderItemStr,OrderId,OrdItemsHashVal)=OEORIItemsHashVal
	.s ret=..Insert(OrderId,UserID,OperationType,OEORIItemsHashVal,MainSignCert,OrderMainSignValue,ExpStr)
    .i +ret'=0  d
	..s ErrLog="签名入库保存出错"
	..d ..SaveVerifySignLog(OrderId,UserID,OperationType,OEORIItemsHashVal,MainSignCert,OrderMainSignValue,ErrLog)
	q ret
}

/// creator:      panshuai
/// Date:         2012-07-13
/// Description:  批量获取时间戳
/// w ##class(web.DHCNurSignVerify).GetBatchTimeStamp()
ClassMethod GetBatchTimeStamp(OEORIItemsXMLStr As %String) As %String
{
	s ^TMP("GetBatchTimeStamp")=OEORIItemsXMLStr
	;s OEORIItemsXMLStr="2Lvb4HCi95di4p+s+0bkshK2sYI=&&&&&&&&&&" //^TMP("GetBatchTimeStamp")
	s BatchTimeStampData=##Class(web.DHCNurCASignVerify).BatchTimeStamp(OEORIItemsXMLStr)
    q BatchTimeStampData
}

/// creator:      panshuai
/// Date:         2012-07-13
/// Description:  记录插入签名过程中的错误日志
/// w ##class(web.DHCNurSignVerify).SaveVerifySignLog()
ClassMethod SaveVerifySignLog(CurrOrderItemStr As %String, UserID As %String, OperationType As %String = "", OEORIMainXMLHash As %String, MainSignCert As %String, MainSignValue As %String, ExpStr As %String = "") As %String
{
	s myrtn=0
	TS
	s Object=##class(User.DHCNurSignVerifyLog).%New()
	s Object.SVLOEOREID=CurrOrderItemStr
	s Object.SVLOEOREMainInfo=OEORIMainXMLHash
	s Object.SVLOEOREMainSignCert=MainSignCert
	s Object.SVLOEOREMainSignValue=MainSignValue
	//s Object.SVLOEOREMainSignTimeStamp=MainSignTimeStamp
	s Object.SVLErrorMsg=ExpStr
	s Object.SVLCreateUserDR=UserID
	s Object.SVLOperationType=OperationType
	s Object.SVLCreateDate=+$H
	s Object.SVLCreateTime=$p($h,",",2)
	s sc=Object.%Save()
	If ($System.Status.IsError(sc))
		{   TRO
			Do $System.Status.DisplayError(sc)
			Set myrtn = "-100"
		}else{
		TC
	    }
    Q myrtn
}

/// creator:      panshuai
/// Date:         2013-12-10
/// Description:  插入签名入表
/// w ##class(web.DHCNurSignVerify).Insert()
ClassMethod Insert(newOrdIdDR As %String, UserID As %String, OperationType As %String = "", OEORIItemHash As %String = "", varCertCode As %String = "", MainSignValue As %String = "", ExpStr As %String = "") As %String
{
	;1.得到医嘱相关的主要信息,其它信息
	;2.将数据组织成XML格式
	;3.插入到User.DHCDocSignVerify
	;4.ExpStr="StopUser_$C(1)_Info_$C(1)_OtherSignCert_$C(1)_OtherSignValue_$C(2)_UpdateUser_$C(1)_Info_$C(1)_OtherSignCert_$C(1)_OtherSignValue_$C(4)_Other"
	;"OSVSignResTypeDR_$C(1)_OSVInfo_$C(1)_OSVSignCert_$C(1)_OSVSignValue_$C(2)_OSVSignResTypeDR_$C(1)_OSVInfo_$C(1)_OSVSignCert_$C(1)_OSVSignValue_$C(4)_Other"
    s $ZT="ERROR^DHCSSERR"
    ;w ##Class(web.DHCDocSignVerify).Insert() 
    
    s ^TMP("InsertSignRecord",1)=newOrdIdDR
	s ^TMP("InsertSignRecord",2)=UserID
	s ^TMP("InsertSignRecord",3)=OperationType
	s ^TMP("InsertSignRecord",4)=OEORIItemHash
	s ^TMP("InsertSignRecord",5)=varCertCode
	s ^TMP("InsertSignRecord",6)=MainSignValue
	//s ^TMP("InsertSignRecord",7)=MainSignTimeStamp
    s myrtn=0
	TS
	s ret=##Class(CA.DigitalSignatureService).Sign(varCertCode,"NUR",OEORIItemHash,MainSignValue,ExpStr)
	s retcode=$p(ret,"^")
	if (+retcode'=1)
	{
		tro 
		q "-100"
	}
	s signID=$p(ret,"^",2)
	;在医嘱状态表保存签名表CA.DigitalSignature 的外键 和 操作类型
	;其它信息,需循环此变量插入到属性
	;s OtherSignRecord=$p(ExpStr,$C(4),1)
	s Object=##class(User.DHCNurExecSign).%New()
	s Object.SVOEOREID=newOrdIdDR
	
	s Object.SVOEOREMainInfo =OEORIItemHash
	s Object.SVOEOREMainSignCert =varCertCode
	s Object.SVOEOREMainSignValue =MainSignValue
	//s Object.SVOEOREMainSignTimeStamp =MainSignTimeStamp
	s Object.SVCreateUserDR=UserID
	s Object.SVOperationType=OperationType
	do Object.SVDigitalSignatureDRSetObjectId(signID)
	s Object.SVCreateDate=+$H
	s Object.SVCreateTime=$p($h,",",2)
	
	s sc=Object.%Save()
	If ($System.Status.IsError(sc))
		{   TRO
			Do $System.Status.DisplayError(sc)
			Set myrtn = "-100"
		}else{
		TC
	    }
	Q myrtn
}

/// creator:      panshuai
/// Date:         2012-07-13
/// Description:  插入批量签名记录-护理病历
/// w ##class(web.DHCNurSignVerify).InsertNurRecBatchSign()
ClassMethod InsertNurRecBatchSign(EpisodeID As %String, UserID As %String, RecType As %String, RecInfosHash As %String, MainSignCert As %String, MainSignValue As %String, ExpStr As %String = "")
{
    
	s ^TMP("InsertNurRecBatchSign",1)=EpisodeID
	s ^TMP("InsertNurRecBatchSign",2)=UserID
	s ^TMP("InsertNurRecBatchSign",3)=RecType
	s ^TMP("InsertNurRecBatchSign",4)=RecInfosHash
	s ^TMP("InsertNurRecBatchSign",5)=MainSignCert
	s ^TMP("InsertNurRecBatchSign",6)=MainSignValue
	/*
	s CurrOrderItemStr=^TMP("InsertBatchSignRecord",1)
	s UserID=^TMP("InsertBatchSignRecord",2)
	s OperationType=^TMP("InsertBatchSignRecord",3)
	s OrdItemsHashVal=^TMP("InsertBatchSignRecord",4)
	s MainSignCert=^TMP("InsertBatchSignRecord",5)
	s MainSignValue=^TMP("InsertBatchSignRecord",6)
	*/
	
	s BatchTimeStamp=..GetBatchTimeStamp(RecInfosHash)
	i BatchTimeStamp=""  d  q "-2"
	.s ErrLog="批量获取时间戳出错" 
	.d ..SaveRecSignLog(EpisodeID,UserID,RecType,RecInfosHash,MainSignCert,MainSignValue,BatchTimeStamp,ErrLog)
	;b ;BatchTimeStamp
	f i=1:1:$l(RecInfosHash,"&&&&&&&&&&") d
	.s RecHash=$p(RecInfosHash,"&&&&&&&&&&",i)
	.s RecMainSignValue=$p(MainSignValue,"&&&&&&&&&&",i)
	.s BatchTimeStamp=$p(BatchTimeStamp,"&&&&&&&&&&") 
	.s ret=..InsertRecSign(EpisodeID,UserID,RecType,RecHash,MainSignCert,RecMainSignValue,BatchTimeStamp,ExpStr)
	.i +ret'=0  d
	..s ErrLog="签名入库保存出错"
	..d ..SaveRecSignLog(EpisodeID,UserID,RecType,RecHash,MainSignCert,RecMainSignValue,BatchTimeStamp,ErrLog) 
	q 0
}

/// creator:      panshuai
/// Date:         2012-07-13
/// Description:  记录插入签名过程中的错误日志-护理病历
/// w ##class(web.DHCNurSignVerify).SaveRecSignLog()
ClassMethod SaveRecSignLog(EpisodeID As %String, UserID As %String, RecType As %String = "", RecInfosHash As %String, MainSignCert As %String, MainSignValue As %String, MainSignTimeStamp As %String, ExpStr As %String = "") As %String
{
	s myrtn=0
	TS
	s Object=##class(User.DHCNurRecSignLog).%New()
	s Object.SVLEpisodeID=EpisodeID
	s Object.SVLRecMainInfo=RecInfosHash
	s Object.SVLRecMainSignCert=MainSignCert
	s Object.SVLRecMainSignTimeStamp=MainSignTimeStamp
	s Object.SVLRecMainSignValue=MainSignValue
	s Object.SVLRecType=RecType
	s Object.SVLCreateUserDR=UserID
	s Object.SVLCreateDate=+$H
	s Object.SVLCreateTime=$p($h,",",2)
	s Object.SVLErrorMsg=ExpStr
	s sc=Object.%Save()
	If ($System.Status.IsError(sc))
		{   TRO
			Do $System.Status.DisplayError(sc)
			Set myrtn = "-100"
		}else{
		TC
	    }
    Q myrtn
}

/// creator:      panshuai
/// Date:         2012-07-13
/// Description:  插入签名入表-护理病历
/// w ##class(web.DHCNurSignVerify).InsertRecSign()
ClassMethod InsertRecSign(EpisodeID As %String, UserID As %String, RecType As %String = "", RecInfoHash As %String = "", MainSignCert As %String = "", MainSignValue As %String = "", MainSignTimeStamp As %String = "", ExpStr As %String = "") As %String
{
	;1.得到医嘱相关的主要信息,其它信息
	;2.将数据组织成XML格式
	;3.插入到User.DHCDocSignVerify
	;4.ExpStr="StopUser_$C(1)_Info_$C(1)_OtherSignCert_$C(1)_OtherSignValue_$C(2)_UpdateUser_$C(1)_Info_$C(1)_OtherSignCert_$C(1)_OtherSignValue_$C(4)_Other"
	;"OSVSignResTypeDR_$C(1)_OSVInfo_$C(1)_OSVSignCert_$C(1)_OSVSignValue_$C(2)_OSVSignResTypeDR_$C(1)_OSVInfo_$C(1)_OSVSignCert_$C(1)_OSVSignValue_$C(4)_Other"
    s $ZT="ERROR^DHCSSERR"
    ;w ##Class(web.DHCDocSignVerify).Insert() 
    
    s ^TMP("InsertRecSign",1)=EpisodeID
	s ^TMP("InsertRecSign",2)=UserID
	s ^TMP("InsertRecSign",3)=RecType
	s ^TMP("InsertRecSign",4)=RecInfoHash
	s ^TMP("InsertRecSign",5)=MainSignCert
	s ^TMP("InsertRecSign",6)=MainSignValue
	s ^TMP("InsertRecSign",7)=MainSignTimeStamp
	
	/*
	s newOrdIdDR=^TMP("InsertSignRecord",1)
	s UserID=^TMP("InsertSignRecord",2)
	s OperationType=^TMP("InsertSignRecord",3)
	s OEORIItemHash=^TMP("InsertSignRecord",4)
	s MainSignCert=^TMP("InsertSignRecord",5)
	s MainSignValue=^TMP("InsertSignRecord",6)
	s MainSignTimeStamp=^TMP("InsertSignRecord",7)
	*/
    s myrtn=0
	;其它信息,需循环此变量插入到属性
	;s OtherSignRecord=$p(ExpStr,$C(4),1)
	TS
	s Object=##class(User.DHCNurRecSign).%New()
	s Object.SVEpisodeID=EpisodeID
	s Object.SVRecMainInfo =RecInfoHash
	s Object.SVRecMainSignCert =MainSignCert
	s Object.SVRecMainSignValue =MainSignValue
	s Object.SVRecMainSignTimeStamp =MainSignTimeStamp
	s Object.SVRecType=RecType
	s Object.SVCreateUserDR=UserID
	s Object.SVCreateDate=+$H
	s Object.SVCreateTime=$p($h,",",2)
	
	s sc=Object.%Save()
	If ($System.Status.IsError(sc))
		{   TRO
			Do $System.Status.DisplayError(sc)
			Set myrtn = "-100"
		}else{
		TC
	    }
	Q myrtn
}

/// 批量验证签名
/// w ##Class(web.DHCDocSignVerify).GetBatchVerifySignedData() 
ClassMethod GetBatchVerifySignedData(BstrCert As %String, BatchInData As %String, BatchsignedData As %String) As %String
{
	s ^TMP("GetOEORIItemsSignedData",1)=BstrCert
	s ^TMP("GetOEORIItemsSignedData",2)=BatchInData
	s ^TMP("GetOEORIItemsSignedData",3)=BatchsignedData
	;s BstrCert=^TMP("GetOEORIItemsSignedData",1)
	;s BatchInData=^TMP("GetOEORIItemsSignedData",2)
	;s BatchsignedData=^TMP("GetOEORIItemsSignedData",3)
	s BatchVerifySignedData=##Class(web.DHCDocCASignVerify).BatchVerifySignedData(BstrCert,BatchInData,BatchsignedData)
    q BatchVerifySignedData
}

/// 需要签名的医嘱串。
/// w ##Class(web.DHCDocSignVerify).GetOEORIItemsXML("636||1","A") 
ClassMethod GetOEORIItemsXML(OrderItemStr As %String, OperationType As %String = "") As %String
{
	q:OrderItemStr="" ""
	s OEORIItemsXMLHash=""
	f i=1:1:$l(OrderItemStr,"^") d
	.s OrderId=$p(OrderItemStr,"^",i)
	.q:OrderId="" 
	.s OrderIdXMLHash=..HashOEORIItemXML(OrderId,OperationType)
	.Q:OrderIdXMLHash=""
	.i OEORIItemsXMLHash="" s OEORIItemsXMLHash=OrderIdXMLHash
	.e  s OEORIItemsXMLHash=OEORIItemsXMLHash_"&&&&&&&&&&"_OrderIdXMLHash
	i OEORIItemsXMLHash'="" s OEORIItemsXMLHash=OEORIItemsXMLHash_"&&&&&&&&&&"
    q OEORIItemsXMLHash
}

/// 哈希医嘱字符串
ClassMethod HashOEORIItemXML(OrderItemID As %String, OperationType As %String = "") As %String
{
	;w ##Class(web.DHCDocSignVerify).HashOEORIItemXML()
	;转成20byte的20-byte SHA-1 hash哈希值
	s stream=##class(%GlobalCharacterStream).%New()
    d stream.Rewind()
    s OEORIMainXML=..GetOEOREItemXML(OrderItemID,OperationType)
    b ;!,OEORIMainXML
    d stream.Write(OEORIMainXML)
	set hash=$System.Encryption.SHA1HashStream(stream)
	b ;
	if hash'=""  s hash=$System.Encryption.Base64Encode(hash)
	;s ^TMP("GetOEORIMainXMLHash")=hash
	q hash
}

}

```