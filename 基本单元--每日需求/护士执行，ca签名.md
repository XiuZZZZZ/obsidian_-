---
日期: 2023-07-24T00:00:00.000+08:00
项目: 眉山妇幼
newColumn7: 王金江
完成: true
更新: true
业务: 护士执行--8.4
---

# 修改

## 文件更新

### 五个cls—第1个

web.DHCNurCASignVerify

```text
Class web.DHCNurCASignVerify Extends %RegisteredObject [ ClassType = "", Not ProcedureBlock ]
{

/// Creator：      panshuai
/// CreatDate：    2012-07-12
/// Description:   判断某科室是否使用CA,1为启用,0为不启用                      
/// Debug:         w ##Class(web.DHCNurCASignVerify).GetIsVerifyCA(102) 
ClassMethod GetIsVerifyCA(LocID As %String) As %String
{
  /*
  s ret=0
  q:'$d(^CANur("Start")) 0
  q:^CANur("Start")'="Y" 0
  i ^CANur("CTLocIDs")[("^"_LocID_"^") s ret=1
  q ret
  */
  q 1
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
  q ret                        //2013-04-02
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

### 五个cls—第2个

web.DHCNurSignVerify

```text
Class web.DHCNurSignVerify Extends (%RegisteredObject, %XML.Adaptor) [ Inheritance = right, Not ProcedureBlock ]
{

// w ##class(web.DHCNurSignVerify).GetUserSignImage("2532")

/// ##class(web.DHCNurSignVerify).GetUserSignImage()
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
/// w ##class(web.DHCNurSignVerify).GetOEOREItemXML()
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
  s:ExecDat'="" ExecDat=$zdh(ExecDat,3)
  s:ExecDat'="" ExecDat=$zd(ExecDat,3)
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
  s Priority=$p(ordstr1,"^",8)       ;医嘱优先级
  s:Priority'="" Priority=$p($G(^OECPR(Priority)),"^",2)
  s ArcimId=$p(ordstr1,"^",2)         ;医嘱名称
  q:ArcimId="" "" ;不存在医嘱项的都是异常医嘱
  s ArcimId1=$p(ArcimId,"||",1),ArcimId2=$p(ArcimId,"||",2)
  s ArcimDesc=$p($G(^ARCIM(ArcimId1,ArcimId2,1)),"^",2)
  s DoseQty=$p(ordstr2,"^",1)         ;单次剂量
  s DoseUnit=$p(ordstr2,"^",3)       ;单次剂量单位
  s:DoseUnit'="" DoseUnit=$p(^CT("UOM",DoseUnit),"^",2)
  s Freq=$p(ordstr2,"^",4)           ;频次
  s:Freq'="" Freq=$P($G(^PHCFR(Freq)),"^",2)
  s Instr=$p(ordstr2,"^",7)       ;用法
  s:Instr'="" Instr=$P($G(^PHCIN(Instr)),"^",2)
  
  s PrescNo=$p(ordstr1,"^",14)       ;处方号
  s OrdStatus=$p(ordstr1,"^",13)       ;医嘱状态
  s:$g(OrdStatus)'="" OrdStatus=$p(^OEC("OSTAT",OrdStatus),"^",2)
  s UserDepartment=$p(ordstr7,"^",2)   ;开单科室
  s:UserDepartment'="" UserDepartment=$p(^CTLOC(UserDepartment),"^",2)
  s RecDep=$p(ordstr3,"^",6)       ;接收科室
  s:RecDep'="" RecDep=$p(^CTLOC(RecDep),"^",2)
  s Doctor=$p(ordstr1,"^",11)         ;下医嘱医生
  s:Doctor'="" Doctor=$p($g(^CTPCP(Doctor,1)),"^",1)
  ;备注
  f rnum=1:1:$g(^OEORD(OeordId,"I",OeoriSub,"DEP",0))  d
  .s Notes=$g(Notes)_$g(^OEORD(OeordId,"I",OeoriSub,"DEP",rnum))
  
  s PhQtyOrd=$p(ordstr1,"^",12)   ;总量
  s BillUOM=$p($g(^ARCIM(ArcimId1,ArcimId2,8)),"^",14) ; 整包娤单位
  s:BillUOM'="" BillUOM=$p($g(^CT("UOM",BillUOM)),"^",2)
  
  s Dura=$p(ordstr2,"^",6)       ;疗程
  s:Dura'="" Dura=$p($G(^PHCDU(Dura)),"^",2)
  s LabEpisodeNo=$p(ordstr3,"^",20)   ;标本号
  s OrdSkinTest=$p(ordstr5,"^",2)      ;皮试

  s UseAdd=$p(ordstr7,"^",1)    ;下医嘱用户
  s:UseAdd'="" UseAdd=$p($g(^SSU("SSUSR",UseAdd)),"^",1)
  s OrdCreateDate=$p(ordstr3,"^",7)    ;下医嘱日期
  s:OrdCreateDate'="" OrdCreateDate=$zd(OrdCreateDate,3)
  s OrdCreateTime=$p(ordstr1,"^",17)   ;下医嘱时间
  s:OrdCreateTime'="" OrdCreateTime=$zt(OrdCreateTime,2)
  
  s StopDoctor=$p(ordstr3,"^",29)    ;停医嘱人
  s:StopDoctor'="" StopDoctor=$p($g(^CTPCP(StopDoctor,1)),"^",1)
  s StopDate=$p(ordstr3,"^",34)     ;停医嘱日期
  s:StopDate'="" StopDate=$zd(StopDate,3)
  s StopTime=$p(ordstr2,"^",15)     ;停医嘱时间
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
    s ^temp("InsertBatchSignRecord")=$lb(CurrOrderItemStr , UserID , OperationType, OrdItemsHashVal , MainSignCert , MainSignValue , ExpStr )
  s ^TMP("InsertBatchSignRecord",1)=CurrOrderItemStr
  s ^TMP("InsertBatchSignRecord",2)=UserID
  s ^TMP("InsertBatchSignRecord",3)=OperationType
  s ^TMP("InsertBatchSignRecord",4)=OrdItemsHashVal
  s ^TMP("InsertBatchSignRecord",5)=MainSignCert
  s ^TMP("InsertBatchSignRecord",6)=MainSignValue
  /*
  s CurrOrderItemStr=^TMP("InsertBatchSignRecord",1)
  s UserID=^TMP("InsertBatchSignRecord",2)
  s OperationType=^TMP("InsertBatchSignRecord",3)
  s OrdItemsHashVal=^TMP("InsertBatchSignRecord",4)
  s MainSignCert=^TMP("InsertBatchSignRecord",5)
  s MainSignValue=^TMP("InsertBatchSignRecord",6)
  */
  s ret=0
  //s ^okki2(CurrOrderItemStr,OrdItemsHashVal,MainSignValue)=UserID
  f i=1:1:$l(CurrOrderItemStr,"^") q:ret'=0  d
  .s OrderId=$p(CurrOrderItemStr,"^",i)
  .q:(OrderId="")
  .//s OEORIItemsHashVal=$p(OrdItemsHashVal,"&&&&&&&&&&",i)
  .//s OrderMainSignValue=$p(MainSignValue,"&&&&&&&&&&",i)
  .s OEORIItemsHashVal=OrdItemsHashVal
  .s OrderMainSignValue=MainSignValue
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
  s MainSignTimeStamp=$p(ret,"^",3)
  ;在医嘱状态表保存签名表CA.DigitalSignature 的外键 和 操作类型
  ;其它信息,需循环此变量插入到属性
  ;s OtherSignRecord=$p(ExpStr,$C(4),1)
  s Object=##class(User.DHCNurExecSign).%New()
  s Object.SVOEOREID=newOrdIdDR
  
  s Object.SVOEOREMainInfo =OEORIItemHash
  s Object.SVOEOREMainSignCert =varCertCode
  s Object.SVOEOREMainSignValue =MainSignValue
  s Object.SVOEOREMainSignTimeStamp =MainSignTimeStamp
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

### 五个cls—第3个

web.DHCNurEntityExecInfo

```text
Class web.DHCNurEntityExecInfo Extends (%RegisteredObject, %XML.Adaptor) [ Inheritance = right ]
{

/// Priority
Property Priority As %String(TRUNCATE = 1);

/// ArcimDesc
Property ArcimDesc As %String(TRUNCATE = 1);

/// DoseQty
Property DoseQty As %String(TRUNCATE = 1);

/// DoseUnit
Property DoseUnit As %String(TRUNCATE = 1);

/// Freq
Property Freq As %String(TRUNCATE = 1);

/// Instr
Property Instr As %String(TRUNCATE = 1);

/// PrescNo
Property PrescNo As %String(TRUNCATE = 1);

/// OrdStatus
Property OrdStatus As %String(TRUNCATE = 1);

/// UserDepartment
Property UserDepartment As %String(TRUNCATE = 1);

/// RecDep
Property RecDep As %String(TRUNCATE = 1);

/// Doctor
Property Doctor As %String(TRUNCATE = 1);

/// Notes
Property Notes As %String(TRUNCATE = 1);

/// PhQtyOrd
Property PhQtyOrd As %String(TRUNCATE = 1);

/// BillUOM
Property BillUOM As %String(TRUNCATE = 1);

/// Dura
Property Dura As %String(TRUNCATE = 1);

/// LabEpisodeNo
Property LabEpisodeNo As %String(TRUNCATE = 1);

/// OrdSkinTest
Property OrdSkinTest As %String(TRUNCATE = 1);

/// UseAdd
Property UseAdd As %String(TRUNCATE = 1);

/// OrdCreateDate
Property OrdCreateDate As %String(TRUNCATE = 1);

/// OrdCreateTime
Property OrdCreateTime As %String(TRUNCATE = 1);

/// StopDoctor
Property StopDoctor As %String(TRUNCATE = 1);

/// StopDate
Property StopDate As %String(TRUNCATE = 1);

/// StopTime
Property StopTime As %String(TRUNCATE = 1);

/// OrdStartDate
Property OrdStartDate As %String(TRUNCATE = 1);

/// OrdStartTime
Property OrdStartTime As %String(TRUNCATE = 1);

/// ExecStatDesc
Property ExecStatDesc As %String(TRUNCATE = 1);

/// ExecNotes
Property ExecNotes As %String(TRUNCATE = 1);

/// ExecDat
Property ExecDat As %String(TRUNCATE = 1);

/// ExecTim
Property ExecTim As %String(TRUNCATE = 1);

/// ExecUser
Property ExecUser As %String(TRUNCATE = 1);

/// ExecType
Property ExecType As %String(TRUNCATE = 1);

}

```

### 五个cls—第4个

User.DHCNurSignVerifyLog

```text
Class User.DHCNurSignVerifyLog Extends (%Persistent, %XML.Adaptor) [ Inheritance = right, Owner = {_SYSTEM}, SqlRowIdName = SVL_Rowid, SqlTableName = DHC_NurSignVerifyLog ]
{

/// 医嘱ID或执行记录ID
Property SVLOEOREID As %String [ SqlColumnNumber = 2, SqlFieldName = SVL_OEOREID ];

/// 执行主要信息原文(使用HASH数据存储)
Property SVLOEOREMainInfo As %String(MAXLEN = 5000) [ SqlColumnNumber = 3, SqlFieldName = SVL_OEOREMainInfo ];

/// 执行主要信息签名证书(仅数据签名服务器验签需要此参数,其它P7和XML都封装在签名信息中)
Property SVLOEOREMainSignCert As %String(MAXLEN = 5000) [ SqlColumnNumber = 4, SqlFieldName = SVL_OEOREMainSignCert ];

/// 执行时间戳
Property SVLOEOREMainSignTimeStamp As %String(MAXLEN = 99999) [ SqlColumnNumber = 5, SqlFieldName = SVL_OEOREMainSignTimeStamp ];

/// 执行主要信息签名信息
Property SVLOEOREMainSignValue As %String(MAXLEN = 5000) [ SqlColumnNumber = 6, SqlFieldName = SVL_OEOREMainSignValue ];

/// 操作类型(1 执行医嘱 2 撤销执行 3 置皮试结果 4 打印执行单 5 打印瓶贴 6 打印条码)
Property SVLOperationType As %String [ SqlColumnNumber = 7, SqlFieldName = SVL_OperationType ];

/// 记录创建人
Property SVLCreateUserDR As %String [ SqlColumnNumber = 8, SqlFieldName = SVL_CreateUser_DR ];

/// 记录创建日期
Property SVLCreateDate As %Date [ SqlColumnNumber = 9, SqlFieldName = SVL_CreateDate ];

/// 记录创建时间
Property SVLCreateTime As %Time [ SqlColumnNumber = 10, SqlFieldName = SVL_CreateTime ];

/// 错误日志
Property SVLErrorMsg As %String(MAXLEN = 500) [ SqlColumnNumber = 11, SqlFieldName = SVL_ErrorMsg ];

Storage Default
{
<Data name="DHCNurSignVerifyLogDefaultData">
<Value name="1">
<Value>%%CLASSNAME</Value>
</Value>
<Value name="2">
<Value>SVLCreateDate</Value>
</Value>
<Value name="3">
<Value>SVLCreateTime</Value>
</Value>
<Value name="4">
<Value>SVLCreateUserDR</Value>
</Value>
<Value name="5">
<Value>SVLErrorMsg</Value>
</Value>
<Value name="6">
<Value>SVLOEOREID</Value>
</Value>
<Value name="7">
<Value>SVLOEOREMainInfo</Value>
</Value>
<Value name="8">
<Value>SVLOEOREMainSignCert</Value>
</Value>
<Value name="9">
<Value>SVLOEOREMainSignTimeStamp</Value>
</Value>
<Value name="10">
<Value>SVLOEOREMainSignValue</Value>
</Value>
<Value name="11">
<Value>SVLOperationType</Value>
</Value>
</Data>
<DataLocation>^User.DHCNurSignVerifyLogD</DataLocation>
<DefaultData>DHCNurSignVerifyLogDefaultData</DefaultData>
<IdLocation>^User.DHCNurSignVerifyLogD</IdLocation>
<IndexLocation>^User.DHCNurSignVerifyLogI</IndexLocation>
<StreamLocation>^User.DHCNurSignVerifyLogS</StreamLocation>
<Type>%Library.CacheStorage</Type>
}

}

```

### 五个cls—第5个

User.DHCNurExecSign

```text
/// CA认证(数字证书认证)
Class User.DHCNurExecSign Extends (%Persistent, %XML.Adaptor) [ Inheritance = right, Owner = {_SYSTEM}, SqlRowIdName = SV_Rowid, SqlTableName = DHC_NurExecSign ]
{

/// 医嘱ID或执行记录ID
Property SVOEOREID As %String [ SqlColumnNumber = 2, SqlFieldName = SV_OEOREID ];

/// 执行主要信息原文(使用HASH数据存储)
Property SVOEOREMainInfo As %String(MAXLEN = 5000) [ SqlColumnNumber = 3, SqlFieldName = SV_OEOREMainInfo ];

/// 执行主要信息签名证书(仅数据签名服务器验签需要此参数,其它P7和XML都封装在签名信息中)
Property SVOEOREMainSignCert As %String(MAXLEN = 5000) [ SqlColumnNumber = 4, SqlFieldName = SV_OEOREMainSignCert ];

/// 执行时间戳
Property SVOEOREMainSignTimeStamp As %String(MAXLEN = 99999) [ SqlColumnNumber = 5, SqlFieldName = SV_OEOREMainSignTimeStamp ];

/// 执行主要信息签名信息
Property SVOEOREMainSignValue As %String(MAXLEN = 5000) [ SqlColumnNumber = 6, SqlFieldName = SV_OEOREMainSignValue ];

/// 操作类型(1 执行医嘱 2 撤销执行 3 置皮试结果 4 打印执行单 5 打印瓶贴 6 打印条码 7 处理医嘱)
Property SVOperationType As %String [ SqlColumnNumber = 7, SqlFieldName = SV_OperationType ];

/// 记录创建人
Property SVCreateUserDR As %String [ SqlColumnNumber = 8, SqlFieldName = SV_CreateUser_DR ];

/// 记录创建日期
Property SVCreateDate As %String [ SqlColumnNumber = 9, SqlFieldName = SV_CreateDate ];

/// 记录创建时间
Property SVCreateTime As %String [ SqlColumnNumber = 10, SqlFieldName = SV_CreateTime ];

/// 指向 CA.DigitalSignature (电子病历接口表关联)
Property SVDigitalSignatureDR As CA.DigitalSignature [ SqlColumnNumber = 11, SqlFieldName = SV_DigitalSignature_DR ];

Index OEOREID On SVOEOREID;

Storage Default
{
<Data name="DHCNurExecSignDefaultData">
<Value name="1">
<Value>%%CLASSNAME</Value>
</Value>
<Value name="2">
<Value>SVCreateDate</Value>
</Value>
<Value name="3">
<Value>SVCreateTime</Value>
</Value>
<Value name="4">
<Value>SVCreateUserDR</Value>
</Value>
<Value name="5">
<Value>SVOEOREID</Value>
</Value>
<Value name="6">
<Value>SVOEOREMainInfo</Value>
</Value>
<Value name="7">
<Value>SVOEOREMainSignCert</Value>
</Value>
<Value name="8">
<Value>SVOEOREMainSignTimeStamp</Value>
</Value>
<Value name="9">
<Value>SVOEOREMainSignValue</Value>
</Value>
<Value name="10">
<Value>SVOperationType</Value>
</Value>
<Value name="11">
<Value>SVDigitalSignatureDR</Value>
</Value>
</Data>
<DataLocation>^User.DHCNurExecSignD</DataLocation>
<DefaultData>DHCNurExecSignDefaultData</DefaultData>
<IdLocation>^User.DHCNurExecSignD</IdLocation>
<IndexLocation>^User.DHCNurExecSignI</IndexLocation>
<StreamLocation>^User.DHCNurExecSignS</StreamLocation>
<Type>%Library.CacheStorage</Type>
}

}

```

### 导入同步的NurseCA.js

```JavaScript
/**
 * @author Songchao  
 * @description  封装调用电子病历CA方法的js
 * @name  NurseCA.js
*/
(function () {
     /**
     * @description  验证ca,弹出模态框
     */
    function CheckCA()
    {
        var result="";
        var rtn = dhcsys_getcacert();
        if (!rtn.IsSucc) 
        {
            result = "error@证书未登录,请重新登录证书!";
        }    
        else
        {
            if (rtn.ContainerName == "")
            {
                result = "error@签名失败,请重新签名!";
            }
            else
            {
                result = rtn.ContainerName;
            }
        }
        return rtn;
    }
     /**
     * @description  对医嘱数据进行签名
     * @param {*} execID 执行记录ID
     * @param {*} skinBatch 皮试批号
     * @param {*} queryTypeCode 单据code
     * @param {*} exedate  执行日期
     * @param {*} exetime  执行时间
     * @param {*} userID  操作人
     */
  function signOrder(execID, skinBatch, queryTypeCode, exedate, exetime, userID, locID,ajax)
  {
      var ret = "0"
    //return ret;
    var CALocFlag = tkMakeServerCall("web.DHCNurCASignVerify","GetIsVerifyCA",locID);
    if (CALocFlag==0) return ret;
        var rtn = dhcsys_getcacert();
        if (!rtn.IsSucc) {
            alert("证书未登录,请重新登录证书!");
            return ;
        }  
        var execInfo = "已执行^" + skinBatch + "^" + exedate + "^" + exetime + "^" + userID + "^" + queryTypeCode;
        ///医嘱信息生成xml串
    if(execID.indexOf("^")>-1)
    {
      var array = execID.split("^");
      for (var i = 0; i < array.length; i ++)
      {
        var ord=array[i];
        
        
        var OEOREItemXML = tkMakeServerCall("web.DHCNurSignVerify","GetOEOREItemXML",ord, execInfo, 1)
        if (rtn.ContainerName!=""){ 
          var hashData = rtn.ca_key.HashData(OEOREItemXML);      ///将医嘱信息生成的XML串做Hash处理
          var signData = rtn.ca_key.SignedData(hashData,rtn.ContainerName);    ///对医嘱数据近行签名操作
          if (signData ==""){
            alert("SignedData 方法失败 签名值为空!");
            return ;
          } else {
            var userCertCode = rtn.CAUserCertCode;  //ca用户唯一标识
            var certNo = rtn.CACertNo;              //ca证书唯一标识

            ret = tkMakeServerCall("web.DHCNurSignVerify", "InsertBatchSignRecord",ord, userID, 1, hashData, userCertCode, signData, certNo);
            if (ret != "0") 
            {
              alert("数字签名没成功");
              return;
            }
          }
        }else{
          return "ca没有登陆";
        }
      }
    }
    else
    {
      var OEOREItemXML = tkMakeServerCall("web.DHCNurSignVerify","GetOEOREItemXML",execID, execInfo, 1)
      if (rtn.ContainerName!=""){
        var hashData = rtn.ca_key.HashData(OEOREItemXML);      ///将医嘱信息生成的XML串做Hash处理
        var signData = rtn.ca_key.SignedData(hashData,rtn.ContainerName);    ///对医嘱数据近行签名操作
        if (signData ==""){
          alert("SignedData 方法失败 签名值为空!");
          return ;
        } else {
          var userCertCode = rtn.CAUserCertCode;  //ca用户唯一标识
          var certNo = rtn.CACertNo;              //ca证书唯一标识

          ret = tkMakeServerCall("web.DHCNurSignVerify", "InsertBatchSignRecord",execID, userID, 1, hashData, userCertCode, signData, certNo);
          if (ret != "0") 
          {
            alert("数字签名没成功");
            return;
          }
        }
      }else{
          return "ca没有登陆";
        }
    }
       return ret;
    }
    var NurseCA = {
        CheckCA: CheckCA,
        signOrder: signOrder
    };
    window.NurseCA = NurseCA;
})()
```

### 导入异步的NurseCA.js

```JavaScript
/**
 * @author Songchao  
 * @description  封装调用电子病历CA方法的js
 * @name  NurseCA.js
*/
(function () {
  

     /**
     * @description  验证ca,弹出模态框
     */
  function CheckCA()
  {
    debugger
    var result="";
    function delRet(rtn){
          if (!rtn.IsSucc) 
          {
              result = "error@证书未登录,请重新登录证书!";
          }    
          else
          {
              if (rtn.ContainerName == "")
              {
                  result = "error@签名失败,请重新签名!";
              }
              else
              {
                  result = rtn.ContainerName;
              }
          }
    }
    var options = {}
    options.callback = delRet 
    var rtn = dhcsys_getcacert(options);
    return "";

  }
     /**
     * @description  对医嘱数据进行签名
     * @param {*} execID 执行记录ID
     * @param {*} skinBatch 皮试批号
     * @param {*} queryTypeCode 单据code
     * @param {*} exedate  执行日期
     * @param {*} exetime  执行时间
     * @param {*} userID  操作人
     */
  
    function signOrder(execID, skinBatch, queryTypeCode, exedate, exetime, userID, locID)
    {
      debugger
      var options = {}
      options.callback = function (rtn){
          if (!rtn.IsSucc) {
              alert("证书未登录,请重新登录证书!");
              return ;
          }
    }
      
    var CALocFlag = tkMakeServerCall("web.DHCNurCASignVerify","GetIsVerifyCA",locID);
    if (CALocFlag==0) return;
        var rtn = dhcsys_getcacert(options);
        if (!rtn.IsSucc) {
            alert("证书未登录,请重新登录证书!");
            return ;
        }  
        var execInfo = "已执行^" + skinBatch + "^" + exedate + "^" + exetime + "^" + userID + "^" + queryTypeCode;
        ///医嘱信息生成xml串
    if(execID.indexOf("^")>-1)
    {
      var array = execID.split("^");
      for (var i = 0; i < array.length; i ++)
      {
        var ord=array[i];
        var OEOREItemXML = tkMakeServerCall("web.DHCNurSignVerify","GetOEOREItemXML",ord, execInfo, 1)
        if (rtn.ContainerName!=""){ 
          var hashData = rtn.ca_key.HashData(OEOREItemXML);      ///将医嘱信息生成的XML串做Hash处理
          var signData = rtn.ca_key.SignedData(hashData,rtn.ContainerName);    ///对医嘱数据近行签名操作
          if (signData ==""){
            alert("SignedData 方法失败 签名值为空!");
            return ;
          } else {
            var userCertCode = rtn.UserID;  //ca用户唯一标识
            var certNo = rtn.CACertNo;              //ca证书唯一标识

            var ret = tkMakeServerCall("web.DHCNurSignVerify", "InsertBatchSignRecord",ord, userID, 1, hashData, userCertCode, signData,certNo);
            if (ret != "0") 
            {
              alert("数字签名没成功");
              return;
            }
          }
        }
      }
    }
    else
    {
      var OEOREItemXML = tkMakeServerCall("web.DHCNurSignVerify","GetOEOREItemXML",execID, execInfo, 1)
      if (rtn.ContainerName!=""){ 
        var hashData = rtn.ca_key.HashData(OEOREItemXML);      ///将医嘱信息生成的XML串做Hash处理
        var signData = rtn.ca_key.SignedData(hashData,rtn.ContainerName);    ///对医嘱数据近行签名操作
        if (signData ==""){
          alert("SignedData 方法失败 签名值为空!");
          return ;
        } else {
          var userCertCode = rtn.CAUserCertCode;  //ca用户唯一标识
          var certNo = rtn.CACertNo;              //ca证书唯一标识

          var ret = tkMakeServerCall("web.DHCNurSignVerify", "InsertBatchSignRecord",execID, userID, 1, hashData, userCertCode, signData, certNo);
          if (ret != "0") 
          {
            alert("数字签名没成功");
            return;
          }
        }
      }
    }
       
    }

    var NurseCA = {
        CheckCA: CheckCA,
        signOrder: signOrder
    };
    window.NurseCA = NurseCA;
})()
```

### 执行的调用的地方

OrderExcute.js

```text

    //20230724 执行地方
      chunks.forEach(function(chunk) {
        /*
        promises.push(
          ajax(
            orderHandleApi.updateOrdGroupChunks,
            skinTestFlag,
            chunk.join("^"),
            execStatusCode,
            userID,
            locID,
            queryTypeCode,
            date,
            time,
            changeReasonDr,
            groupID
          ).then(function(result) {
            if (String(result.success) !== "0") {
              errors = errors.concat(result.errList);
            }
          })
        );*/
        window.NurseCA.signOrder(chunk.join("^"),"",queryTypeCode,date,time,userID,locID)
      });

```

## 医嘱单后台

### 修改findLong

### 修改findTemp

## 医嘱单c#程序

### 后台增加dll和dao方法

```JavaScript
public static string GetUserSignImage(string userId)
{
    string result = DoctorOrderSheetDAO.GetUserSignImage(userId);
    return result;
}

public static string GetUserSignImage(string userId)
{
    return DHCCConnection.DHCCConnection.getData("web.DHCNurSignVerify", "GetUserSignImage", userId);
}
```

### 后台增加drawUser方法

```JavaScript
public void drawUser(Graphics g, DoctorOrderCellData cellData, DoctorOrderColumnConfig columnConfig, ref bool ifDraw)
{
    if (cellData.Value.IndexOf("*") < 0)
    {
        this.drawCell(g, cellData, columnConfig, ref ifDraw);
        return;
    }
    string userName = cellData.Value.Split('*')[0];
    string userId = cellData.Value.Split('*')[1];
    if (this.signPrintFlag && (cellData.PrintingFlag == false))
    {
        return;
    }
    float width = columnConfig.Width;
    float height = columnConfig.ContentHeight;
    float x = cellData.X;
    float y = cellData.Y;
    Brush brush = columnConfig.Brush;
    if (this.seePrintFlag)

    {

        if (cellData.PrintingFlag)
        {
            brush = columnConfig.ViewBrush;
        }

    }
    else
    {
        x = x - this.hardMarginX;
        y = y - this.hardMarginY;
    }
    Color color = ((System.Drawing.SolidBrush)brush).Color;

    if (userId != "")
    {
        Image image = null;
        if (userId.IndexOf("/") > -1)
        {

            string[] users = userId.Split('/');
            try
            {
                image = GetUserImageColored(users[0], color);
                if (image == null)
                {
                    cellData.Value = cellData.Value.Split('*')[0];
                    this.drawCell(g, cellData, columnConfig, ref ifDraw);
                }
                else
                {
                    g.DrawImage(image, x, y, (width / 2)-1, height);
                }

                image = GetUserImageColored(users[1], color);
                if (image == null)
                {
                    cellData.Value = cellData.Value.Split('*')[0];
                    this.drawCell(g, cellData, columnConfig, ref ifDraw);
                }
                else
                {
                    g.DrawImage(image, x +1+ width / 2, y, width / 2, height);
                }
            }
            catch
            {
            }

        }
        else
        {
            try
            {
                image = GetUserImageColored(userId, color);
                if (image == null)
                {
                    cellData.Value = cellData.Value.Split('*')[0];
                    this.drawCell(g, cellData, columnConfig, ref ifDraw);
                }
                else
                {
                    g.DrawImage(image, x, y, width, height);
                }
            }
            catch
            {
            }

        }

    }
    ifDraw = true;
}
public Image StringToImage(string imgstr)
{ //字符串－－〉流
    if (imgstr == "") return null;
    byte[] ChangeAfterImageBytes = Convert.FromBase64String(imgstr);//再将字符串分拆成字节数组
    MemoryStream ChangeAfterMS = new MemoryStream(ChangeAfterImageBytes);//将字节数组保存到新的内存流上
    Image Image = Image.FromStream(ChangeAfterMS);//将内存流保存成一张图片
    return Image;
}
public Image GetUserImageColored(string userid, Color color)
{
    string userpic = DoctorOrderSheetBLL.GetUserSignImage(userid);
    Image signImage = StringToImage(userpic);
    if (signImage != null)
    {
        signImage = this.changecolor(signImage, color.R, color.G, color.B);
    }
    return signImage;
}
public Bitmap changecolor(Image SImage, int l1, int l2, int l3)
{
    int Height = SImage.Height;
    int Width = SImage.Width;
    Bitmap bitmap = new Bitmap(Width, Height);
    Bitmap MyBitmap = (Bitmap)SImage;
    for (int x = 0; x < Width; x++)
    {
        for (int y = 0; y < Height; y++)
        {
            Color pixel1 = MyBitmap.GetPixel(x, y);
            int r = pixel1.R;
            int g = pixel1.G;
            int b = pixel1.B;
            if (r + g + b < 250 * 3)
            {
                r = l1;
                g = l2;
                b = l3;
                if (r > 255)
                {
                    r = 255;
                }
                if (r < 0)
                {
                    r = 0;
                }
                if (g > 255)
                {
                    g = 255;
                }
                if (g < 0)
                {
                    g = 0;
                }
                if (b > 255)
                {
                    b = 255;
                }
                if (b < 0)
                {
                    b = 0;
                }
                bitmap.SetPixel(x, y, Color.FromArgb(r, g, b));
            }
        }
    }
    return bitmap;
}
```

### 更改DoctorPrintData.cs drawOrder方法

```JavaScript
public void drawOrder(Graphics g,int page)
{
    if (this.printData.ContainsKey(page))
    {
        DoctorOrderPageData pageData = this.printData[page];
        if (pageData != null)
        {
            foreach (int rowNo in pageData.pageData.Keys)
            {
                DoctorOrderRowData rowData = pageData.pageData[rowNo];
                bool ifDraw = false;
                foreach (string columnName in rowData.rowData.Keys)
                {
                    DoctorOrderCellData cellData = rowData.rowData[columnName];
                    if (cellData.Value == "")
                    {
                        continue;
                    }
                    DoctorOrderColumnConfig columnCofig;
                    if ((cellData.Code == "ArcimDesc")&&(rowData.NextPageFlag))
                    {
                        columnCofig = this.config["NextPage"];
                    }
                    else
                    {
                        columnCofig = this.config[columnName];
                    }
                    switch (cellData.Code)
                    {
                        case "Doctor":
                        case "SeeNurse":
                        case "ExcuteNurse":
                        case "StopDoctor":
                        case "StopNurse":
                            this.drawUser(g, cellData, columnCofig, ref ifDraw);
                            break;
                        default:
                            this.drawCell(g, cellData, columnCofig, ref ifDraw);
                            break;
                    }
                }
                if (ifDraw)
                {
                    this.printHistoryData.Add(rowData);
                }
            }
        }
    }
}
```

# 其它

## 医嘱单打印逻辑

```text
获取数据
  类：DoctorOrderSheet
  方法：initOrderAllLocs
    装载数据
    类：DoctorLocOrder
      DoctorPrintData对象 PrintData
      arrangeDate
        this.printData.initData(this.records, this.orders,false,"");
        this.arrangeDataFlag = true;
  
打印
  类：DoctorPrintData
  方法：drawOrder

类：DoctorPrintData
属性对象：public Dictionary<int, DoctorOrderPageData> printData;
```

## 三个远程

```JavaScript
黔西南
420490681
123456

滇东北
671009861
dhcc1234

眉山
601508466
884358
```

## 护士执行的批量插入

```JavaScript
/// Description:  插入批量签名记录-执行医嘱
/// w ##class(web.DHCNurSignVerify).InsertBatchSignRecord()
```

## 判断某科室是否使用CA,1为启用,0为不启用

```text
/// Description: 判断某科室是否使用CA,1为启用,0为不启用
/// Debug: w ##Class(web.DHCNurCASignVerify).GetIsVerifyCA(102)
```

# End