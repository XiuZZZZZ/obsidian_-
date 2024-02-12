

## main

- 过程
	- 获取图标的方法
		- ##class(Nur.NIS.Service.Base.Patient).GetChunkIcons("4404084^4270917")
- 修改
	- [[#新增入口方法]]
		- [[#新增护理病历取值方法]]
	- [[#新增图标定义]]
	- 新增图标管理


## 新增护理病历取值方法

```
/// 获取指定普通录入单的指定数据
/// ##class(Nur.Z.Need.NurMp).GetBasicNurMpData(4404084,"DHCNURHZTTPGDLR","Item207")
ClassMethod GetBasicNurMpData(EpisodeId, Indentity, ItemIdStr)
{
	s result = ""
	s IndentityU = $ZCONVERT(Indentity,"U")
	s TCIndentityId = $o(^NurMp.TemplateI("Indentity"," "_IndentityU,""))
	i TCIndentityId'=""{
		s TCIndentity = $list(^NurMp.TemplateD(TCIndentityId),4)
		s TCIndentityU = $ZCONVERT(TCIndentity,"U")
		s num = 1
		s rowId="" f{
			s rowId = $o(^NurMp.DHCTempMultDataI("EmrCode"," "_TCIndentityU," "_EpisodeId,rowId),-1)
			q:rowId=""
			q:result'=""
			//是否作废
			s cancelUser = $list(^NurMp.DHCTempMultDataD(rowId),75)
			continue:cancelUser'=""
			s obj = ##class(NurMp.DHCTempMultData).%OpenId(rowId)
			f itemIdIndex=1:1:$l(ItemIdStr,"^"){
				s itemId = $p(ItemIdStr,"^",itemIdIndex)
				s value = $ZOBJPROPERTY(obj,itemId)
				s value = ..GetValue(value)
				s result = value
			}
		}
	}
	q result
}

ClassMethod GetValue(value)
{
	
	i value="[]" q ""
	i value'["[{" q value
	i value["[{"{
		s retValue = ""
		s itemValue=##class(NurMp.Json).Decode(value)
		s itemValuekey=itemValue.Next("")
		while (itemValuekey'="")
		{
			s Text=itemValue.GetAt(itemValuekey).GetAt("Text")
			i retValue=""  s retValue=Text
			e  s retValue=retValue_ItmlbValue_Text
			s itemValuekey=itemValue.Next(itemValuekey)
		}
		s value = retValue
	}
	q value
}
```

## 新增入口方法

```
/// ##class(web.DHCSETIMAGE).ifPainLevel(4404084,"轻度疼痛")
ClassMethod ifPainLevel(episodeId, desc)
{
	n (episodeId,desc)
	//如果有疼痛遍数据获取过，制指定分钟后才查
	i $d(^DHCSETIMAGE(episodeId,"PainLevel")){
		s lastDateTimeL = ^DHCSETIMAGE(episodeId,"PainLevel","dateTimeL")
		s currDateTimeL = +$h*100000+$p($h,",",2)
		i (currDateTimeL-lastDateTimeL)>1{
			s ^DHCSETIMAGE(episodeId,"PainLevel","dateTimeL") = currDateTimeL
			s ^DHCSETIMAGE(episodeId,"PainLevel","desc") = ##class(Nur.Z.Need.NurMp).GetBasicNurMpData(episodeId,"DHCNURHZTTPGDLR","Item207")
		}
	}else{
	//没有痛疼数据获取过，直接获取
		s ^DHCSETIMAGE(episodeId,"PainLevel","dateTimeL") = +$h*100000+$p($h,",",2)
		s ^DHCSETIMAGE(episodeId,"PainLevel","desc") = ##class(Nur.Z.Need.NurMp).GetBasicNurMpData(episodeId,"DHCNURHZTTPGDLR","Item207")
	}
	//获得这个人的疼痛等级，后续及其当前图标获取
	
	i $d(^DHCSETIMAGE(episodeId,"PainLevel","desc")){
		q:(^DHCSETIMAGE(episodeId,"PainLevel","desc")=desc) 1
	}
	q 0
}
```

## 新增图标定义

代码：painLevel
描述：疼痛
图标：webemr/疼痛.png
条件表达式：s img=##class(web.DHCSETIMAGE).ifPainLevel($g(EpisodeID),"疼痛")
