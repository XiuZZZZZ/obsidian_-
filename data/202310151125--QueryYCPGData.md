```
/// 指定病区，指定单子（压疮）, 指定日期范围内的数据
/// 
/// d ##class(%ResultSet).RunQuery("Nur.Custom.NeedUnit.NeedUnit230825","QueryYCPGData","2023-08-24","2023-08-25","","DHCNURSTYSPGJSYJLD")
Query QueryYCPGData(StartDate, StartTime, EndDate, EndTime, WardId, Indentity) As websys.Query(ROWSPEC = "episodeId,regNo,locDesc,patName,bedDesc,medicareNo,inHospDateTimeH,visitStatusDesc,tools,startDateTime,endDateTime")
{
}

ClassMethod QueryYCPGDataExecute(ByRef qHandle As %Binary, StartDate, StartTime, EndDate, EndTime, WardId, Indentity) As %Status
{
	set repid=$I(^CacheTemp)
 	s ind=1
 	s qHandle=$lb(0,repid,0)
 	s startDateL = ##Class(websys.Conversions).DateHtmlToLogical(StartDate)
 	s startTimeL = ##Class(websys.Conversions).TimeHtmlToLogical(StartTime)
 	s startDateTimeL = startDateL*100000+startTimeL
 	s endDateL = ##Class(websys.Conversions).DateHtmlToLogical(EndDate)
 	s endTimeL = ##Class(websys.Conversions).TimeHtmlToLogical(EndTime)
 	s endDateTimeL = endDateL*100000+endTimeL
 	
 	// 获取指定病区，时间范围内就诊的病人就诊号  ^||episodeIdSort (value)，因为有些日期不是CareDate，没有索引
 	d ..GetEpisodeIdSort(WardId,StartDate,EndDate)
	// 获得时间范围内，指定病区病人的，^||episodeIdSort(value,"NurMpIdStr")
	d ..GetNurMpRowIdStr(Indentity)
	// 根据id返回数据即可
	s episodeId = "" f{
		s episodeId = $o(^||episodeIdSort(episodeId))
		q:episodeId=""
		// 获得病人在所有病区的时间范围
		//^||TransInfoSort(23,6)="6670242420^6670243195"
		//^||TransInfoSort(34,999)="6670243195^6671158212"
		k ^||TransInfoSort
		i WardId'="" d ##class(Nur.Custom.NeedUnit.NeedUnit230825).GetTransInfoSort(episodeId)

		s idStr = ^||episodeIdSort(episodeId,"NurMpIdStr")
		continue:idStr=""
		s idLen = $l(idStr,"^")
		f i=1:1:idLen{
			s id = $p(idStr,"^",i)
			s obj = ##class(NurMp.DHCTempMultData).%OpenId(id)
			
			s date = $ZOBJPROPERTY(obj,"CareDate")
			s time = $ZOBJPROPERTY(obj,"CareTime")
			s dateL = ##Class(websys.Conversions).DateHtmlToLogical(date)
			s timeL = ##Class(websys.Conversions).TimeHtmlToLogical(time)
			s dateTimeL = dateL*100000+timeL
			i WardId'="" {
				s ifDateTimeFlag = 0
				s transId = "" f{
					s transId = $o(^||TransInfoSort(WardId,transId))
					q:transId=""
					s transStartDateTimeL = $p(^||TransInfoSort(WardId,transId),"^",1)
					s transeEndDateTimeL = $p(^||TransInfoSort(WardId,transId),"^",2)
					i (dateTimeL>transStartDateTimeL)&&(dateTimeL<transeEndDateTimeL){
						s ifDateTimeFlag = 1
					}
				}
				continue:ifDateTimeFlag=0
			}
			continue:(dateTimeL<startDateTimeL)||(dateTimeL>endDateTimeL)
			
			//获取数据
			//--------------------------------------------------------
			d OutQueryYSPGData
		}
		
	}

	Set qHandle=$lb(0,repid,0)
	Quit $$$OK
OutQueryYSPGData
	set Data=$lb(episodeId,regNo,locDesc,patName,bedDesc,medicareNo,inHospDateTimeH,visitStatusDesc,tools,startDateTime,endDateTime)
 	Set ^CacheTemp(repid,ind)=Data
 	Set ind=ind+1
	quit
}
```