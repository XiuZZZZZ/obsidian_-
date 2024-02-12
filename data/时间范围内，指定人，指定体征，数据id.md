```js

/// w ##class(Nur.Custom.Temperature).getMRData2(1143,"temperature","2022-07-23","02:00:00","2022-07-23","02:00:00")
/// 37.2
/// ##class(Nur.Custom.Temperature).getMRData2(1143,"temperature","2022-07-23","00:00:00","2022-07-24","00:00:00")
/// 37.2^37.6^37.10^37.14^36.18^38.22
ClassMethod getMRDataByTimeArea(EpisodeId, code, StartDate, StartTime, EtartDate, EndTime)
{
	s StartDate = ##Class(websys.Conversions).DateHtmlToLogical(StartDate)
	s StartTime = ##Class(websys.Conversions).TimeHtmlToLogical(StartTime)
	s StartDateTime = StartDate*100000+StartTime
	s EtartDate = ##Class(websys.Conversions).DateHtmlToLogical(EtartDate)
	s EndTime = ##Class(websys.Conversions).TimeHtmlToLogical(EndTime)
	s EndDateTime = EtartDate*100000+EndTime
	s result = ""
	s mrAdmId=$P(^PAADM(EpisodeId),"^",61)
	s itemId = $o(^MRC("OBITM",0,"HOSPCode",2,$$$ALPHAUP(code),""))
	f indexDate=StartDate:1:EtartDate {
		i indexDate=StartDate s indexTime=StartTime-1
		e  s indexTime=""
		f{
			s indexTime = $o(^MR(mrAdmId,"OBS",0,"Item",itemId,indexDate,indexTime))
			q:indexTime=""
			//如果不是在查询时间范围内
			s indexDateTime = indexDate*100000+indexTime
			q:(indexDateTime<StartDateTime)||(indexDateTime>EndDateTime)
			s obsId = $o(^MR(mrAdmId,"OBS",0,"Item",itemId,indexDate,indexTime,""))
			s data = ^MR(mrAdmId,"OBS",obsId)
			s value = $p(data,"^",2)
			s outData = value
			s result = $s(result="":outData,1:result_"^"_outData)
		}
	}
	q result
}


```