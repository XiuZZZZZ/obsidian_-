

# 过程

## 远程号

376 292 962
dn0fee2p

## 几个单子的标准数据

```




【自理能力】
单子code：DHCNURHZZLNLPFBLR3
单子类型：里面多个日期时间列数据
itemDate = ""
itemTime =  ""
itemValueSort(1) = "Item43,Item44,Item55"
itemValueSort(2) = "Item58,Item59,Item130"
itemValueSort(3) = "Item60,Item65,Item131"
itemValueSort(4) = "Item61,Item66,Item132"
itemValueSort(5) = "Item62,Item67,Item133"
itemValueSort(6) = "Item63,Item68,Item134"
itemValueSort(7) = "Item64,Item69,Item135"
判断标准：分支大于好多


【非计划拔管】
单子code：DHCNURZYHZFJHBGWXYSPGJYFCS
单子类型：里面多个日期时间列数据
s itemDate = ""
s itemTime = ""
s itemValueSort(1) = "CareDate,CareTime,Item94"
s itemValueSort(2) = "Item10,Item14,Item95"
s itemValueSort(3) = "Item11,Item15,Item96"
s itemValueSort(4) = "Item13,Item16,Item97"
s itemValueSort(5) = "Item12,Item17,Item98"
判断标准：

【营养风险性的高危】
单子code：DHCNURZYHZNRSYYFXPGJGYCSJLD
单子类型：普通录入单
itemDate：Item8
itemTime：Item24
itemValue：Item14
判断标准：
```

## 提供普通录入单的统计方法

## 提供多列数据录入单的统计方法

# 修改

## 增加方法

```
/// ##class(Nur.SHIFT.Service.ShiftMethods).ProcessNurMp2()
/// 普通录入单和多行录入单都可以
ClassMethod ProcessNurMp2(TJDate, WardID, EpisodeID, StartDateTime, EndDateTime, InPatDateTime, OutPatDateTime = "", Indentity)
{
	i EpisodeID=50370 s ^temp("ProcessNurMp2",StartDateTime)=$lb(TJDate, WardID, EpisodeID, StartDateTime, EndDateTime, InPatDateTime, OutPatDateTime, Indentity)
	s StartDateL = $p(StartDateTime,",",1)
	s StartTimeL = $p(StartDateTime,",",2)
	s EndDateL = $p(EndDateTime,",","1")
	s EndTimel = $p(EndDateTime,",","2")
	s StartDateH = ##Class(websys.Conversions).DateLogicalToHtml(StartDateL)
	s StartTimeH = ##Class(websys.Conversions).TimeLogicalToHtml(StartTimeL)
	s EndDateH = ##Class(websys.Conversions).DateLogicalToHtml(EndDateL)
	s EndTimeH = ##Class(websys.Conversions).TimeLogicalToHtml(EndTimel)
	s StartDateL = ##Class(websys.Conversions).DateHtmlToLogical(StartDateH)
	s StartTimeL = ##Class(websys.Conversions).TimeHtmlToLogical(StartTimeH)
	s EndDateL = ##Class(websys.Conversions).DateHtmlToLogical(EndDateH)
	s EndTimeL = ##Class(websys.Conversions).TimeHtmlToLogical(EndTimeH)

	s StartDateTimeL = StartDateL*100000+StartTimeL
	s EndDateTimeL = EndDateL*100000+EndTimeL
	
	
	
	f i=1:1:$l(Indentity,"@"){
		s oneIndentity = $p(Indentity,"@",i)
		s sc = ##class(Nur.Custom.NeedUnit.NurMpToShift).IfValid(EpisodeID,oneIndentity,StartDateTimeL,EndDateTimeL)
		q:sc=1
	}
	
	q sc
}
```

## 增加方法
```
Class Nur.Custom.NeedUnit.NurMpToShift Extends %RegisteredObject
{

/// $zdh("2023-11-16",3) 66794
/// $zth(10) 36000
/// $zth(18) 64800
/// ##class(Nur.Custom.NeedUnit.NurMpToShift).IfValid(50370,"DHCNURZYHZNRSYYFXPGJGYCSJLD","6679436000","6679464800")
ClassMethod IfValid(EpisodeId, Indentity, StartDateTimeL, EndDateTimeL)
{
	s result = "3"
	s rowIdStr = ##class(Nur.Custom.NeedUnit.NurMpToShift).GetNurMpIdStr(EpisodeId,Indentity)
	i rowIdStr=""{
		s result=3
		q result
	}
	s scoreLevel = ^NurMpToShift("itemValueSort",Indentity,"baseData","scoreLevel")
	s condition = ^NurMpToShift("itemValueSort",Indentity,"baseData","condition")
	s rowId = "" f i=1:1:$l(rowIdStr,"^"){
		s rowId = $p(rowIdStr,"^",i)
		q:(rowId="")||(result=1)
		//判断是否有效
		s lineIndex = 0 f{
			s lineIndex = $o(^NurMpToShift("itemValueSort",Indentity,"lineIndex",lineIndex))
			q:(lineIndex="")||(result=1)
			s dateItem = $p(^NurMpToShift("itemValueSort",Indentity,"lineIndex",lineIndex),",",1)
			s timeItem = $p(^NurMpToShift("itemValueSort",Indentity,"lineIndex",lineIndex),",",2)
			s valueItem = $p(^NurMpToShift("itemValueSort",Indentity,"lineIndex",lineIndex),",",3)
			s obj = ##class(NurMp.DHCTempMultData).%OpenId(rowId)
			s date = $ZOBJPROPERTY(obj,dateItem)
			s time = $ZOBJPROPERTY(obj,timeItem)
			s dateL = ##Class(websys.Conversions).DateHtmlToLogical(date)
			s timeL = ##Class(websys.Conversions).TimeHtmlToLogical(time)
			continue:(dateL="")||(timeL="")
			s dateTimeL = dateL*100000+timeL
			
			s score = $ZOBJPROPERTY(obj,valueItem)
			//判断
			s judgeRet = ..Judge(StartDateTimeL,EndDateTimeL,dateTimeL,score,condition,scoreLevel)
			i judgeRet=1 s result=1
		}
	}
	//遍历每个id，获得
	q result
}

/// 1代表成功，0代表失败
/// 判断时间范围内，是否满足条件
/// 满足时间后，判断是否值满足【只有两个条件>=和<=】
ClassMethod Judge(StartDateTimeL, EndDateTimeL, DateTimeL, score, condition, scoreLevel)
{
	s judgeRet = 0
	q:((DateTimeL<StartDateTimeL)||(DateTimeL>EndDateTimeL)) judgeRet
	if (condition="<="){
		if (score<=scoreLevel){
			s judgeRet=1
		}
	}elseif(condition=">="){
		if (score>=scoreLevel){
			s judgeRet=1
		}
	}
	q judgeRet
}

/// ##class(Nur.Custom.NeedUnit.NurMpToShift).GetNurMpIdStr(50370,"DHCNURZYHZNRSYYFXPGJGYCSJLD")
ClassMethod GetNurMpIdStr(EpisodeId, Indentity)
{
	s rowIdStr = ""
	s IndentityU = $ZCONVERT(Indentity,"U")
	s TCIndentityId = $o(^NurMp.TemplateI("Indentity"," "_IndentityU,""))
	i TCIndentityId'=""{
		s TCIndentity = $list(^NurMp.TemplateD(TCIndentityId),4)
		s TCIndentityU = $ZCONVERT(TCIndentity,"U")
		s rowId="" f{
			s rowId = $o(^NurMp.DHCTempMultDataI("EmrCode"," "_TCIndentityU," "_EpisodeId,rowId))
			q:rowId=""
			//是否作废
			s cancelUser = $list(^NurMp.DHCTempMultDataD(rowId),75)
			continue:cancelUser'=""
			s rowIdStr = $s(rowIdStr="":rowId,1:rowIdStr_"^"_rowId)
		}
	}
	q rowIdStr
}

/// ##class(Nur.Custom.NeedUnit.NurMpToShift).InitData()
ClassMethod InitData()
{
	k ^NurMpToShift
	//【vte外】
	//单子code：DHCNURwkzyhzjmxsx
	//单子类型：里面多个日期时间列数据
	s ^NurMpToShift("itemValueSort","DHCNURwkzyhzjmxsx","lineIndex",1) = "CareDate,CareTime,Item345"
	s ^NurMpToShift("itemValueSort","DHCNURwkzyhzjmxsx","lineIndex",2) = "Item18,Item19,Item346"
	s ^NurMpToShift("itemValueSort","DHCNURwkzyhzjmxsx","lineIndex",3) = "Item20,Item21,Item347"
	s ^NurMpToShift("itemValueSort","DHCNURwkzyhzjmxsx","lineIndex",4) = "Item22,Item23,Item348"
	s ^NurMpToShift("itemValueSort","DHCNURwkzyhzjmxsx","lineIndex",5) = "Item24,Item25,Item349"
	s ^NurMpToShift("itemValueSort","DHCNURwkzyhzjmxsx","lineIndex",6) = "Item26,Item27,Item350"
	s ^NurMpToShift("itemValueSort","DHCNURwkzyhzjmxsx","lineIndex",7) = "Item28,Item29,Item351"
	//判断标准：>=5
	s ^NurMpToShift("itemValueSort","DHCNURwkzyhzjmxsx","baseData","scoreLevel")=5
	s ^NurMpToShift("itemValueSort","DHCNURwkzyhzjmxsx","baseData","condition")=">="

	//【vte内】
	//单子code：DHCNURNKZYHZJMXSSSZFXPGJYFCSB3
	//单子类型：里面多个日期时间列数据
	s ^NurMpToShift("itemValueSort","DHCNURNKZYHZJMXSSSZFXPGJYFCSB3","lineIndex",1) = "CareDate,CareTime,Item86"
	s ^NurMpToShift("itemValueSort","DHCNURNKZYHZJMXSSSZFXPGJYFCSB3","lineIndex",2) = "Item23,Item24,Item87"
	s ^NurMpToShift("itemValueSort","DHCNURNKZYHZJMXSSSZFXPGJYFCSB3","lineIndex",3) = "Item25,Item28,Item88"
	s ^NurMpToShift("itemValueSort","DHCNURNKZYHZJMXSSSZFXPGJYFCSB3","lineIndex",4) = "Item26,Item29,Item89"
	s ^NurMpToShift("itemValueSort","DHCNURNKZYHZJMXSSSZFXPGJYFCSB3","lineIndex",5) = "Item27,Item30,Item90"
	//判断标准：>=4
	s ^NurMpToShift("itemValueSort","DHCNURNKZYHZJMXSSSZFXPGJYFCSB3","baseData","scoreLevel")=4
	s ^NurMpToShift("itemValueSort","DHCNURNKZYHZJMXSSSZFXPGJYFCSB3","baseData","condition")=">="

	//【压力性损伤评估】
	//单子code：DHCNURylxpgjhlcsb
	//单子类型：里面多个日期时间列数据
	s ^NurMpToShift("itemValueSort","DHCNURylxpgjhlcsb","lineIndex",1) = "CareDate,CareTime,Item81"
	s ^NurMpToShift("itemValueSort","DHCNURylxpgjhlcsb","lineIndex",2) = "Item16,Item17,Item82"
	s ^NurMpToShift("itemValueSort","DHCNURylxpgjhlcsb","lineIndex",3) = "Item18,Item19,Item83"
	s ^NurMpToShift("itemValueSort","DHCNURylxpgjhlcsb","lineIndex",4) = "Item20,Item21,Item84"
	s ^NurMpToShift("itemValueSort","DHCNURylxpgjhlcsb","lineIndex",5) = "Item22,Item23,Item85"
	//判断标准：<=12
	s ^NurMpToShift("itemValueSort","DHCNURylxpgjhlcsb","baseData","scoreLevel")=12
	s ^NurMpToShift("itemValueSort","DHCNURylxpgjhlcsb","baseData","condition")="<="

	//【自理能力】
	//单子code：DHCNURHZZLNLPFBLR3
	//单子类型：里面多个日期时间列数据
	s ^NurMpToShift("itemValueSort","DHCNURHZZLNLPFBLR3","lineIndex",1) = "Item43,Item44,Item55"
	s ^NurMpToShift("itemValueSort","DHCNURHZZLNLPFBLR3","lineIndex",2) = "Item58,Item59,Item130"
	s ^NurMpToShift("itemValueSort","DHCNURHZZLNLPFBLR3","lineIndex",3) = "Item60,Item65,Item131"
	s ^NurMpToShift("itemValueSort","DHCNURHZZLNLPFBLR3","lineIndex",4) = "Item61,Item66,Item132"
	s ^NurMpToShift("itemValueSort","DHCNURHZZLNLPFBLR3","lineIndex",5) = "Item62,Item67,Item133"
	s ^NurMpToShift("itemValueSort","DHCNURHZZLNLPFBLR3","lineIndex",6) = "Item63,Item68,Item134"
	s ^NurMpToShift("itemValueSort","DHCNURHZZLNLPFBLR3","lineIndex",7) = "Item64,Item69,Item135"
	//判断标准：<=40
	s ^NurMpToShift("itemValueSort","DHCNURHZZLNLPFBLR3","baseData","scoreLevel")=40
	s ^NurMpToShift("itemValueSort","DHCNURHZZLNLPFBLR3","baseData","condition")="<="


	//【非计划拔管】
	//单子code：DHCNURZYHZFJHBGWXYSPGJYFCS
	//单子类型：里面多个日期时间列数据
	s ^NurMpToShift("itemValueSort","DHCNURZYHZFJHBGWXYSPGJYFCS","lineIndex",1) = "CareDate,CareTime,Item94"
	s ^NurMpToShift("itemValueSort","DHCNURZYHZFJHBGWXYSPGJYFCS","lineIndex",2) = "Item10,Item14,Item95"
	s ^NurMpToShift("itemValueSort","DHCNURZYHZFJHBGWXYSPGJYFCS","lineIndex",3) = "Item11,Item15,Item96"
	s ^NurMpToShift("itemValueSort","DHCNURZYHZFJHBGWXYSPGJYFCS","lineIndex",4) = "Item13,Item16,Item97"
	s ^NurMpToShift("itemValueSort","DHCNURZYHZFJHBGWXYSPGJYFCS","lineIndex",5) = "Item12,Item17,Item98"
	//判断标准：>8
	s ^NurMpToShift("itemValueSort","DHCNURZYHZFJHBGWXYSPGJYFCS","baseData","scoreLevel")=9
	s ^NurMpToShift("itemValueSort","DHCNURZYHZFJHBGWXYSPGJYFCS","baseData","condition")=">="


	//【营养风】
	//单子code：DHCNURZYHZNRSYYFXPGJGYCSJLD
	//单子类型：普通录入单
	s ^NurMpToShift("itemValueSort","DHCNURZYHZNRSYYFXPGJGYCSJLD","lineIndex",1) = "Item12,CareTime,Item14"
	//判断标准：>=3
	s ^NurMpToShift("itemValueSort","DHCNURZYHZNRSYYFXPGJGYCSJLD","baseData","scoreLevel")=3
	s ^NurMpToShift("itemValueSort","DHCNURZYHZNRSYYFXPGJGYCSJLD","baseData","condition")=">="
}

}

```

## 维护项目绑定统计方法

![[Pasted image 20231116171157.png]]

