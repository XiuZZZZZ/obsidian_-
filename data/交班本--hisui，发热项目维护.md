

# 过程

## 远程号

376 292 962
dn0fee2p

## 确定索引

^MR(mrRid,"OBS",0,"Item",itemId,date,time,id)
## 写方法

### 新增方法

```
Class Nur.Custom.NeedUnit.TemperatureToShift Extends %RegisteredObject
{

/// 判断是否日期时间范围内，存在指定条件数据
/// ##class(Nur.Custom.NeedUnit.TemperatureToShift).IfValid(EpisodeId, Type, StartDateL, StartTimeL, EndDateL, EndTimeL)
ClassMethod IfValid(EpisodeId, Type, StartDateL, StartTimeL, EndDateL, EndTimeL)
{
	s StartDateTimeL = StartDateL*100000+StartTimeL
	s EndDateTimeL = EndDateL*100000+EndTimeL
	s result = 3
	//遍历指定
	if Type=1{
		s itemId = 69
		//^MR(mrRid,"OBS",0,"Item",itemId,dateL,timeL,id)
		s mrId = $P(^PAADM(EpisodeId),"^",61)
		f dateL=StartDateL:1:EndDateL{
			q:result=1
			s timeL = "" f{
				q:result=1
				s timeL = $o(^MR(mrId,"OBS",0,"Item",itemId,dateL,timeL))
				q:timeL=""
				s rowId = $o(^MR(mrId,"OBS",0,"Item",itemId,dateL,timeL,""))
				s dateTimeL = dateL*100000+timeL
				continue:rowId=""
				continue:(dateTimeL<StartDateTimeL)||(dateTimeL>EndDateTimeL)
				s value = $p(^MR(mrId,"OBS",rowId),"^",2)
				if (value)&&(value>37.3){
					s result = 1
				}

			}
		}
	}
	q result
}

}

```

### 新增方法

```
/// 发热
/// Nur.SHIFT.Service.ShiftMethods
ClassMethod ProcessTemperature(TJDate, WardID, EpisodeID, StartDateTime, EndDateTime, InPatDateTime, OutPatDateTime = "", Type)
{
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
	
	
	
	s sc = ##class(Nur.Custom.NeedUnit.TemperatureToShift).IfValid(EpisodeID,Type,StartDateL,StartTimeL,EndDateL,EndTimeL)
	
	q sc
}
```
## 维护方法

![[Pasted image 20231121003050.png]]

# 修改

## 新增方法--统计方法
```
Class Nur.Custom.NeedUnit.TemperatureToShift Extends %RegisteredObject
{

/// 判断是否日期时间范围内，存在指定条件数据
/// ##class(Nur.Custom.NeedUnit.TemperatureToShift).IfValid(EpisodeId, Type, StartDateL, StartTimeL, EndDateL, EndTimeL)
ClassMethod IfValid(EpisodeId, Type, StartDateL, StartTimeL, EndDateL, EndTimeL)
{
	s StartDateTimeL = StartDateL*100000+StartTimeL
	s EndDateTimeL = EndDateL*100000+EndTimeL
	s result = 3
	//遍历指定
	if Type=1{
		s itemId = 69
		//^MR(mrRid,"OBS",0,"Item",itemId,dateL,timeL,id)
		s mrId = $P(^PAADM(EpisodeId),"^",61)
		f dateL=StartDateL:1:EndDateL{
			q:result=1
			s timeL = "" f{
				q:result=1
				s timeL = $o(^MR(mrId,"OBS",0,"Item",itemId,dateL,timeL))
				q:timeL=""
				s rowId = $o(^MR(mrId,"OBS",0,"Item",itemId,dateL,timeL,""))
				s dateTimeL = dateL*100000+timeL
				continue:rowId=""
				continue:(dateTimeL<StartDateTimeL)||(dateTimeL>EndDateTimeL)
				s value = $p(^MR(mrId,"OBS",rowId),"^",2)
				if (value)&&(value>37.3){
					s result = 1
				}

			}
		}
	}
	q result
}

}
```

## 新增方法--入口方法

```
/// 发热
/// Nur.SHIFT.Service.ShiftMethods
ClassMethod ProcessTemperature(TJDate, WardID, EpisodeID, StartDateTime, EndDateTime, InPatDateTime, OutPatDateTime = "", Type)
{
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
	
	
	
	s sc = ##class(Nur.Custom.NeedUnit.TemperatureToShift).IfValid(EpisodeID,Type,StartDateL,StartTimeL,EndDateL,EndTimeL)
	
	q sc
}
```

## 维护方法

![[Pasted image 20231121003050.png]]

