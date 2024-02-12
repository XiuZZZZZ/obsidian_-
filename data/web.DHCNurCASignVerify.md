

```
Class web.DHCNurCASignVerify Extends %RegisteredObject [ ClassType = "", Not ProcedureBlock ]
{

/// Creator：      panshuai
/// CreatDate：    2012-07-12
/// Description:   判断某科室是否使用CA,1为启用,0为不启用                      
/// Debug:         w ##Class(web.DHCNurCASignVerify).GetIsVerifyCA(102) 
ClassMethod GetIsVerifyCA(LocID As %String) As %String
{
	//q 0
	/*
	s ret=0
	q:'$d(^CANur("Start")) 0
	q:^CANur("Start")'="Y" 0
	i ^CANur("CTLocIDs")[("^"_LocID_"^") s ret=1
	q ret
	*/
	
	//取全院配置时注释上面代码
	s ret=0
	s ret=##Class(CA.DigitalSignatureService).IsCACTLoc(LocID)
	q ret
}

// GetCAServiceStatus

ClassMethod GetCAServiceStatus(LocID As %String) As %String
{

	s ret=0
	s ret=##Class(CA.DigitalSignatureService).GetCAServiceStatus(LocID)
	q ret
}

ClassMethod ISCAActive(Rowid As %String) As %String
{
	s flag=0
  s Rowids=$g(^CF("CA","CACTLocIDs"))
  s len=$l(Rowids,"^")
  s i=0 f  s i=i+1 q:(i>len)||(flag=1)  d
  .i ($p($g(Rowids),"^",i)=Rowid) s flag=1
  q flag
}

/// Creator：      panshuai
/// CreatDate：    2012-07-12
/// Description:   调用CA的webservice接口
/// w ##Class(web.DHCNurCASignVerify).GetCAService()
ClassMethod GetCAService() As dhcserviceclient.CA.ServiceSoap
{
	set error = 0
	s service=""
	//通过系统参数找到服务地址
	//s url=##Class(EPRmeta.SysOption).GetOptionValueByName("CALocation")
	s url="http://128.8.16.36/CAService/services/CAService?wsdl"
	//s url="http://localhost/ca/Service.asmx"
    // 创建对象，设置服务地址
    s service=##Class(dhcserviceclient.CA.ServiceSoap).%New()
    s service.Location=url
    q service
}

/// Creator：      panshuai
/// CreatDate：    2012-07-12
/// Description:   CA初始化Session                        
/// w ##Class(web.DHCNurCASignVerify).CASessionDataInit() 
ClassMethod CASessionDataInit() As %String
{
   
	set $ZTRAP = "SOAPError"
	d ##Class(CA.BJCAService).CASessionDataInit() 
	q %session.Data("ServerRan")_"@"_%session.Data("ServerSignedData")_"@"_%session.Data("ServerCert")
 
SOAPError

  quit ""
}

/// Creator：      panshuai
/// CreatDate：    2012-07-12
/// Description:   完成登录验证。通过该方法，可以实现验证用户证书，验证签名，然后获取用户证书的唯一标识。
/// Table：        
/// Input：        string bstrCert客户端证书, 隐含元素
///                string InData  服务器端随机数 session, 
///                string signedData 客户端签名,隐含元素
/// Output：       如个人用户，返回SF+身份证号码
///                -1为不信任机构，-2为超过有效期，-3为作废证书，-4为已加入黑名单    
/// Debug:         w ##Class(web.DHCNurCASignVerify).VerifyCertAndSignedData() 
ClassMethod VerifyCertAndSignedData(argbstrCert As %String, argInData As %String, argsignedData As %String) As %String
{
	
	
	s ^TMP("VerifyCertAndSignedData",1)=argsignedData
	s ^TMP("VerifyCertAndSignedData",2)=argInData
	s ^TMP("VerifyCertAndSignedData",3)=argbstrCert
	;s argsignedData=^TMP("VerifyCertAndSignedData",1)
	;s argInData=^TMP("VerifyCertAndSignedData",2)
	;s argbstrCert=^TMP("VerifyCertAndSignedData",3)
	s rtnstr="",rtn=-1
	set $ZTRAP = "SOAPError5"
	s service=##Class(web.DHCNurCASignVerify).GetCAService()
	if $IsObject(service)
	 {
	   s rtnstr = service.VerifyCertAndSignedData(argbstrCert,argInData,argsignedData) 
		i (rtnstr=-1) s rtn=rtnstr,errstr="不信任机构"
		else  if (rtnstr=-2) s rtn=rtnstr,errstr="超过有效期"
		else  if (rtnstr=-3) s rtn=rtnstr, errstr="作废证书"
		else  if (rtnstr=-4) s rtn=rtnstr, errstr="已加入黑名单"
		else  if (($f(rtnstr, "JJ")>0)||($f(rtnstr, "SF")>0)) s rtn=0,errstr=$p(rtnstr,"SF",2)
		else  s errstr="验证失败"
	 }else{
		s errstr="证书错误" 
	 }
	q rtn_"^"_errstr
SOAPError5
  quit "-1^服务异常"
}

/// 服务器端签名验证 调用对方的服务
/// 登录login
/// w ##Class(web.DHCDocCASignVerify).CAServiceVerify()
ClassMethod CAServiceVerify(strContainerName As %String, varCert As %String, strServerRan As %String, strClientSignedData As %String, strPicData As %String, varCertCode As %String = "", certificateNo As %String = "", UserID As %String = "") As %String
{
	s svrRan = %session.Get("ServerRan")
	s ^TMP("CAServiceVerify",1)=strContainerName
	s ^TMP("CAServiceVerify",2)=strClientSignedData
	s ^TMP("CAServiceVerify",3)=strServerRan
	s ^TMP("CAServiceVerify",4)=varCert
	s ^TMP("CAServiceVerify",5)=svrRan
	s ^TMP("CAServiceVerify",6)=strPicData
	//Login
	s uID = ##Class(CA.UsrSignatureInfo).GetUsrIdByKey(varCertCode)
	s ^oee=uID_"%"_UserID
	q:uID'=UserID "数字证书与当前登陆用户不匹配"
	//usrID , UsrCertCode , serverRan , userSignedData , certNo , userCert 
	s info=##Class(CA.DigitalSignatureService).Login(UserID,varCertCode,svrRan,strClientSignedData,certificateNo,varCert)
	//s ^okk=info
	q:info'="" info
	q ""
}

/// 服务器端签名验证 调用对方的服务
/// w ##Class(web.DHCDocCASignVerify).CAServiceVerify()
ClassMethod InsertNurRecBatchSign(EpisodeID As %String, UserID As %String, RecType As %String, RecInfosHash As %String, MainSignCertCode As %String, MainSignCert As %String, MainSignValue As %String, ExpStr As %String = "", NurRecId As %String) As %String
{
	s svrRan = %session.Get("ServerRan")

	//Login
	//UsrCertCode , code , contentHash , signValue 
	s ^okk3=MainSignCertCode_"^"_"NUR"_"^"_RecInfosHash_"^"_MainSignValue
	s info=##Class(CA.DigitalSignatureService).Sign(MainSignCertCode,"NUR",RecInfosHash,MainSignValue)
	s ret=$p(info,"^")
	s err=$p(info,"^",2)
	s ^TMP("wwqwq")=err
	i ret=0 s ret=err
	s ^okk2=info
	i ret="0"  d  q info
	.s ErrLog="获取时间戳出错" 
	.d ..SaveRecSignLog(EpisodeID,UserID,RecType,RecInfosHash,MainSignCert,MainSignValue,BatchTimeStamp,ErrLog)
	;b ;BatchTimeStamp
	i ret="1" d
	.s ID=err
	.s aa=##Class(CA.DigitalSignature).%OpenId(ID)
	.s BatchTimeStamp=aa.SignTimeStamp
	.s ret=..InsertRecSign(EpisodeID,UserID,RecType,RecInfosHash,MainSignCert,MainSignValue,BatchTimeStamp,ExpStr,ID,NurRecId)
	.s ^TMP("2211ww")=ret
	.i +ret'=0  d
	..s ErrLog="签名入库保存出错"
	..d ..SaveRecSignLog(EpisodeID,UserID,RecType,RecInfosHash,MainSignCert,MainSignValue,BatchTimeStamp,ErrLog) 

	q 1
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
ClassMethod InsertRecSign(EpisodeID As %String, UserID As %String, RecType As %String = "", RecInfoHash As %String = "", MainSignCert As %String = "", MainSignValue As %String = "", MainSignTimeStamp As %String = "", ExpStr As %String = "", ID As %String, NurRecId As %String) As %String
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
	s Object.SVDigitalSignatureId=ID
	s Object.SVNurRecId=NurRecId
	s Object.SVEpisodeID=EpisodeID
	s Object.SVRecMainInfo =RecInfoHash
	s Object.SVRecMainSignCert =MainSignCert
	s Object.SVRecMainSignValue =MainSignValue
	s Object.SVRecMainSignTimeStamp =MainSignTimeStamp
	s Object.SVRecType=RecType
	s Object.SVCreateUserDR=UserID
	s Object.SVCreateD=+$h
	s Object.SVCreateT=$p($h,",",2)
	
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

/// Creator：      panshuai
/// CreatDate：    2012-07-12
/// Description:   服务器端签名验证 调用对方的服务
/// w ##Class(web.DHCNurCASignVerify).CAServiceVerify()
ClassMethod CAServiceVerifyold(strContainerName As %String, varCert As %String, strServerRan As %String, strClientSignedData As %String, strPicData As %String) As %String
{
	s svrRan = %session.Get("ServerRan")
	s ^TMP("CAServiceVerify",1)=strContainerName  //容器号(证书编号)
	s ^TMP("CAServiceVerify",2)=strClientSignedData
	s ^TMP("CAServiceVerify",3)=strServerRan //初始化的随机数
	s ^TMP("CAServiceVerify",4)=varCert //证书
	s ^TMP("CAServiceVerify",5)=svrRan //初始化的随机数
	s ^TMP("CAServiceVerify",6)=strPicData  //签名图片
	/*
	s strContainerName=^TMP("strContainerName")
	s strClientSignedData=^TMP("strClientSignedData")
	s strServerRan=^TMP("strServerRan")
	s varCert=^TMP("varCert")
	s svrRan=^TMP("svrRan")*/

	if (strServerRan'=svrRan)
	{
		q "证书会话已过期，请重新认证"
	}
	//s ret = ##Class(web.DHCNurCASignVerify).VerifyCertAndSignedData(varCert,strServerRan,strClientSignedData)
	s ret = ##Class(CA.DigitalSignatureService).VerifyCertAndSignedData(varCert,strServerRan,strClientSignedData)  //2013-04-02
	s ^objcyf1=ret                //2013-04-02
	q ret	                      //2013-04-02
	s retcode=$p(ret,"^")
	s retVal=$p(ret,"^",2)
	q:(retcode'=0) retVal
	s CredNo=retVal,CreduID=""
	s UserID=%session.Data("LOGON.USERID")  //2013-04-02
	set uID = ##Class(CA.UsrSignatureInfo).GetInUseID(UserID)   //2013-04-02
	;1.首先用容器号找用户,如果没有关联用户,可能为KEY升级,所以得用"SF+身份证号"的唯一标识取关联表里查找用户是否注册
	;如果找到记录的话,证明就是KEY升级,自动向用户关联表里插入一条关联记录
	//set uID = ##Class(CA.UsrSignatureInfo).GetUsrIdByCertNo(strContainerName)
	if (uID="")
	{
	  ;set CreduID = ##Class(CA.UsrSignatureInfo).GetUsrIdByCredNo(CredNo)
	  ;s ret=0
	  ;if CreduID'=""  d
	  ;.s ret=..InsertUsrSignatureInfo(CreduID,retVal,varCert,strContainerName,strPicData)
	  ;else  s ret=-1 
	  s ret=-1 
	  q:ret'=0 "证书未被启用"   //2013-04-7 启用
	}else{
	  s UserID=%session.Data("LOGON.USERID")
	  q:uID'=UserID "数字证书与当前登陆用户不匹配"   //2013-04-7 启用
	}
	;Set %session.Data("ContainerName") =strContainerName
	q ""
}

/// Creator：      panshuai 
/// CreatDate：    2012-07-13
/// Description:   批量针对签名数据加盖时间戳       
/// Debug:         w ##Class(web.DHCNurCASignVerify).BatchTimeStamp() 
ClassMethod BatchTimeStamp(argsignData As %String) As %String
{
	s rtnstr=""
	set $ZTRAP = "SOAPError7"
	s service=##Class(web.DHCDocCASignVerify).GetCAService()
	if $IsObject(service)
	 { 
	   s rtnstr = service.BatchTimeStamp(argsignData)
	   q rtnstr
	 }
	q ""
SOAPError7
  quit ""
}

/// Creator：      panshuai 
/// CreatDate：    2012-07-13
/// Description:   第一次认证后把ukey的容器号写到IE的Session中      
/// Debug:         w ##Class(web.DHCNurCASignVerify).GenUKeySession()
ClassMethod GenUKeySession(strContainerName As %String) As %String
{
	q:strContainerName="" 0
	s %session.Data("ContainerName")=strContainerName
	q 1
}

/// Creator：      panshuai 
/// CreatDate：    2012-07-13
/// Description:   判断是否Ukey是否改变或者认证过,如果认证过则不需要再次认证  
/// output:        0 表示未认证过或认证过期/改变  1表示不需再认证    
/// Debug:         w ##Class(web.DHCNurCASignVerify).VerifyUKeySession("806000116893002/5303201711002257")
ClassMethod VerifyUKeySession(CurContainerName As %String) As %String
{
	q:CurContainerName="" 0
	q:'$d(%session.Data("ContainerName")) 0
	s oldContainerName=%session.Data("ContainerName")
	q:(oldContainerName'=CurContainerName) 0
	q 1
}

ClassMethod GetRandom(service) As %String
{
  set $ZTRAP = "SOAPError3"
    if $IsObject(service)
   { 
   	   s strServerRan=serivce.GenRandom()
   }
   q $g(strServerRan)  
SOAPError3
  quit ""
}

/// 
/// Creator：      guobaoping
/// CreatDate：    2011-08-26
/// Description:   批量签名       
/// Debug:         w ##Class(web.DHCDocCASignVerify).BatchVerifySignedData() 
ClassMethod BatchVerifySignedData(bstrCert As %String, BatchInData As %String, BatchsignedData As %String) As %String
{
	s rtnstr=""
	set $ZTRAP = "SOAPError6"
	s service=##Class(web.DHCDocCASignVerify).GetCAService()
	if $IsObject(service)
	 { b ;dd
	   s rtnstr = service.BatchVerifySignedData(bstrCert,BatchInData,BatchsignedData)
	   q rtnstr
	 }
	q "-1"
SOAPError6
  quit "-2"
}

// 插入签名用户关联表

/// d ##Class(web.DHCDocCASignVerify).InsertUsrSignatureInfo()
ClassMethod InsertUsrSignatureInfo(CreduID As %String, CredNo As %String, strCert As %String, strContainerName As %String, strPicData As %String)
{
	s ^TMP("InsertUsrSignatureInfo",1)=CreduID
	s ^TMP("InsertUsrSignatureInfo",2)=strCert
	s ^TMP("InsertUsrSignatureInfo",3)=strContainerName
	s ^TMP("InsertUsrSignatureInfo",4)=strPicData
	s myrtn=0
	s obj=##Class(CA.UsrSignatureInfo).%New()
	s obj.IdentityID=CredNo
	s obj.UsrID=CreduID
	s obj.Certificate=strCert
	s obj.CertificateNo=strContainerName
	s obj.SignImage=strPicData
	s obj.CreateDate=$p($h, ",", 1)
	s obj.CreateTime=$p($h, ",", 2)
	s obj.InUse=1
    s sc=obj.%Save()
    If ($System.Status.IsError(sc))
     {
		Do $System.Status.DisplayError(sc)
		Set myrtn = "-100"
     }	
	q myrtn
}

// 验证签名

ClassMethod VerifyCASign(EmrCode As %String, EpisodeID As %String) As %String
{
 s flag=""
 s flag=$O(^User.DHCNurRecSignI("AdmTyp"," "_EpisodeID," "_EmrCode,flag))
 i flag="" d
 .s ret="未保存"	
 e  d
 .s id=""
 .s id=$O(^User.DHCNurRecSignI("AdmTyp"," "_EpisodeID," "_EmrCode,id),-1)
 .s info=##Class(User.DHCNurRecSign).%OpenId(id)
 .s signID=info.SVDigitalSignatureId
 .s contentHash=info.SVRecMainInfo
 .s ret = ##Class(CA.DigitalSignatureService).VerifySign(signID,contentHash)
 q ret
}

ClassMethod SignInfo(EmrCode As %String, EpisodeID As %String) As %String
{
 s flag=""
 s flag=$O(^Nur.DHCMoudDataI("EmrCode"," "_EmrCode," "_EpisodeID,flag))
 i flag="" d
 .s ret="未保存"	
 e  d
 .s id=""
 .s id=$O(^Nur.DHCMoudDataI("EmrCode"," "_EmrCode," "_EpisodeID,id),-1)
 .s info=##Class(Nur.DHCMoudData).%OpenId(id)
 .s SignName=info.Item100
 .s SignDate=info.Item24
 .s SignTime=info.Item49
 .s ret="签名人："_SignName_"   签名时间："_SignDate_" "_SignTime_"   已签名"
 q ret
}

// 提交病历归档

ClassMethod GetZt(EpisodeID As %String, UserID As %String) As %String
{
 s ret=##class(DHCEPRFS.BI.BIToNurse).SetFinalAccountFlag(EpisodeID,UserID)
}

/// 判断当前ukey是否是用户绑定ukey
ClassMethod GetUid(strTemp, UserId) As %String
{
	s ^LLM("dsd")=$lb(strTemp,UserId)
	q:strTemp="" 
	s date="",ret=""
	s len=$l(strTemp,"&&&")
	i len'=2 d
	.f num=1:1:len d
	..s date=$p($p(strTemp,"&&&",num),"||",1)
	..i ret="" s ret=date
	..e  s ret=ret_"^"_date
	e  s ret=$p($p(strTemp,"&&&",1),"||",1)
	s id=""
	s id=$o(^CA.UsrSignatureInfoI("InUseID"," "_UserId,1,id))
	&sql(select CertName into :CertName from CA.UsrSignatureInfo where ID=:id)
	i ret[CertName q 0
	e  q 1
	q
}

}

```