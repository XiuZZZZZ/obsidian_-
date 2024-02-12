```js
/// 1-循环获得的通用数据
/// 2-通用数据，的基础上做一定限制（方向，个数，满足时间范围）
/// 3-如果要优化，就需要索引或者别的逻辑
/// ##class(Nur.Custom.DLL).GetTemperatureValues(4404084,14,1,1)
ClassMethod GetTemperatureValues(EpisodeId, ItemId, Direction = 1, MaxNum = "", StartDate = "", StartTime = "", EndDate = "", EndTime = "")
{
	s result = ""
	s mrAdmId=$P(^PAADM(EpisodeId),"^",61)
	s (startDateTimeL,endDateTimeL) = ""
	s startDateL = ##Class(websys.Conversions).DateHtmlToLogical(StartDate)
	s startTimeL = ##Class(websys.Conversions).TimeHtmlToLogical(StartTime)
	i startDateL'="" s startDateTimeL = startDateL*100000+startTimeL
	
	s endDateL = ##Class(websys.Conversions).DateHtmlToLogical(EndDate)
	s endTimeL = ##Class(websys.Conversions).TimeHtmlToLogical(EndTime)
	i endDateL'="" s endDateTimeL = endDateL*100000+endTimeL
	
	s getMax = ""
	s num = ""
	s indexDate = "" f{
		s indexDate = $o(^MR(mrAdmId,"OBS",0,"Item",ItemId,indexDate),Direction)
		q:(indexDate="")||(getMax'="")
		s indexTime = "" f{
			s indexTime = $o(^MR(mrAdmId,"OBS",0,"Item",ItemId,indexDate,indexTime),Direction)
			q:(indexTime="")||(getMax'="")
			s indexDateTime = indexDate*100000+indexTime
			//如果不是在查询时间范围内
			q:(startDateTimeL'="")&&((indexDateTime<startDateTimeL)||(indexDateTime>endDateTimeL))
			s obsId = $o(^MR(mrAdmId,"OBS",0,"Item",ItemId,indexDate,indexTime,""))
			s data = ^MR(mrAdmId,"OBS",obsId)
			s value = $p(data,"^",2)
			s outData = value
			s num = num + 1
			i (MaxNum'="")&&(num>=MaxNum) s getMax = 1
			
			s result = $s(result="":outData,1:result_"^"_outData)
		}

	}
	q result
}
```