
# 增加方法
```
/// desc：获得病人时间段是否有生命体征，有返回1，无返回0
/// ##class(Nur.Data.UnitMethod).ifHasData(19484,66273,64800)
ClassMethod ifHasData(episodeId, dateL, timeL)
{
	s result = 0
	s mrId = $P(^PAADM(episodeId),"^",61)
	//^MR(1,"OBS",0,"DateTime",65043,61266,1)
	s dateTimeL = dateL*100000+timeL
	s startDateTimeL = dateTimeL - 7200
	s endDateTimeL = dateTimeL + 7200
	f date = $e(startDateTimeL,1,5):1:$e(endDateTimeL,1,5){
		s time = ""
		f{
			s time = $o(^MR(mrId,"OBS",0,"DateTime",date,time))
			q:time=""
			s currDateTime = date*100000+time
			i (currDateTime>=startDateTimeL)&&(currDateTime<=endDateTimeL){
				s result = 1
			}
		}
	}
	q result
}

```

# 修改方法
```
/// Author: SongChao
/// Date: 2020年2月21日 上午10:29:54
/// Description:  产生事件文字对象
/// Input: tags
/// Other: ##Class(Nur.NIS.Service.Chart.DAO.Temperature).generateEventText()
ClassMethod generateEventText2(EpisodeId, ChartDr, EventSub, EventDesc, AdmDate, CurrDate, Time, TextsList As %ListOfDataTypes) As Nur.NIS.Service.Chart.Base.Text
{
    Set pageDays=##class(Nur.NIS.Service.Chart.DAO.Chart).getDays(ChartDr)  
    Set firstTimeP=##class(Nur.NIS.Service.Chart.DAO.Chart).getFirstTimePoint(ChartDr)
    Set times=##class(Nur.NIS.Service.Chart.DAO.Chart).getTimes(ChartDr)
    Set eventGlobal=^CT.NUR.NIS.ChartD(ChartDr,"ChildChartEvent",EventSub)
    Set ifShowTime=$lg(eventGlobal,3)
    Set xAxis=$lg(eventGlobal,6)
    Set yAxis=$lg(eventGlobal,7)
    Set fontFamily=$lg(eventGlobal,9)
    Set fontSize=$lg(eventGlobal,10)
    Set fontStyle=$lg(eventGlobal,11)
    Set color=$lg(eventGlobal,12)
    Set cellWidth=$lg(eventGlobal,15)
    Set cellHeight=$lg(eventGlobal,16)
    Set timeFormat=$lg(eventGlobal,17)
    Set:timeFormat="" timeFormat="Chinese"
    //Set txtDirection=$lg(eventGlobal,13)
    Set timePonit=##class(Nur.NIS.Service.Chart.DAO.Temperature).nearTPoint(Time,ChartDr)
    Set timeP=..calcTimePos(AdmDate, CurrDate, pageDays, firstTimeP, times, timePonit)
    //如果是死亡，且这个时间范围内有生命体征，往后加固定格子大小
    s ifHasData = 0
    i (EventDesc="死亡") {
		s ifHasData = ##class(Nur.Data.UnitMethod).ifHasData(EpisodeId,CurrDate,timePonit)
	}
    i ifHasData="1" s timeP = timeP + 1
    Set px=xAxis+(cellWidth*(timeP\1))

    Set curPage=(CurrDate-AdmDate)\pageDays+1 //事件对应第几页
    While ($g(^||chartevent(curPage,px))=px){
        Set px=px+cellWidth
    }
    Set ^||chartevent(curPage,px)=px

    If ifShowTime="Y" {
        Set intervalMode=$lg(eventGlobal,14)
        Set Time=##Class(Nur.NIS.Service.Chart.Draw.Utils).timeTransChinese(Time,timeFormat)
        Set EventDesc=EventDesc_intervalMode_Time
    }
    Set txtLen=$l(EventDesc)
    Set textCount=1
    For index=1:1:txtLen {
        Set txtStr=$e(EventDesc,index)
        If $ISVALIDNUM(txtStr) {
	        Set index=index+1
	        Set txtStr=txtStr_$e(EventDesc,index)
	    }
        Set textY=yAxis+((textCount-1)*cellHeight)
        Set text=##class(Nur.NIS.Service.Chart.Base.Text).%New(txtStr, px, textY,$g(fontFamily), $g(fontSize), $g(fontStyle), $g(color),$g(cellWidth),"Center")
        Do TextsList.Insert(text.toArray())
        Set textCount=textCount+1
    }
}

```

# 修改方法2
```
ClassMethod iteratorEvent(EpisodeID, Page, ChartDr) As %ListOfDataTypes
{
    Set textsList=##class(%ListOfDataTypes).%New()
    Do ##class(Nur.NIS.Service.Chart.DAO.Event).QtPatAdmDate(EpisodeID)   
    Do ##class(Nur.NIS.Service.Chart.DAO.Event).SyncEventByOrder(EpisodeID)
    ;Set chartDr=##class(Nur.NIS.Service.Chart.DAO.Chart).getChart(EpisodeID)
    Set admDate=..getAdmDate(EpisodeID) ;$p($g(^PAADM(EpisodeID,"DHC")),"^",31)
    Set admTime=$p($g(^PAADM(EpisodeID,"DHC")),"^",32)
    Set mrAdmID=$P(^PAADM(EpisodeID),"^",61)
    Set pageDays=##class(Nur.NIS.Service.Chart.DAO.Chart).getDays(ChartDr)
    Set sdate=admDate+((Page-1)*pageDays)
    Set endDate=sdate+pageDays-1
    s ID=0  f  s ID=$o(^DHCADMQTREC("adm",EpisodeID,ID)) q:ID=""  d
    .s typeID=$p(^DHCADMQTREC("QTREC",ID),"^",4)
    .q:typeID=""
    .s subId=$o(^CT.NUR.NIS.ChartEventI("EventUnique",ChartDr,typeID,""))
    .q:subId=""
    .s eventDate=$p(^DHCADMQTREC("QTREC",ID),"^",2)
    .q:eventDate<sdate
    .q:eventDate>endDate  
    .s eventTime=$P($g(^DHCADMQTREC("QTREC",ID)),"^",3)
    .i $d(event(eventDate,eventTime))  s eventTime=eventTime_"A"
    .s eventDesc=$p($g(^DHCQTRECTYP("typ",typeID)),"^",2)
    .s event(eventDate,eventTime,subId)=eventDesc

    s eventDate=""  f  s eventDate=$o(event(eventDate)) q:eventDate=""  d
    .s eventTime=""  f  s eventTime=$o(event(eventDate,eventTime)) q:eventTime=""  d
    ..s subId="" f  s subId=$o(event(eventDate,eventTime,subId)) q:subId=""  d
    ...s eventDesc=event(eventDate,eventTime,subId)
    ...d ..generateEventText2(EpisodeID,ChartDr, subId, eventDesc,admDate, eventDate,+eventTime,textsList)
    Quit textsList
}

```