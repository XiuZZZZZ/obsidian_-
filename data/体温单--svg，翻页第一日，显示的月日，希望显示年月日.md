

```
/// Author: SongChao
/// Date: 2019-12-18 16:06:15
/// Description: 获取测量日期出院后显示到当前页的最后一天
/// Input: 
/// Return: 
/// Other:  ##Class(Nur.NIS.Service.Chart.DAO.Expression).getMeasureDateToPageEnd(2,447,"2020-07-18")
ClassMethod getMeasureDateToPageEnd(ChartDr, EpisodeID, MeasureDate) As %String
{
    New (ChartDr,EpisodeID,MeasureDate)
    Set MeasureDate=..%Zdh(MeasureDate)
    Set date=""  
    Set dischgDate=$p(^PAADM(EpisodeID),"^",17)
	Set admDate=$p($g(^PAADM(EpisodeID,"DHC")),"^",31)
    Set:admDate="" admDate=$p($g(^PAADM(EpisodeID)),"^",6)
    //Set chartDr=##class(Nur.NIS.Service.Chart.DAO.Chart).getChart(EpisodeID)
    Set pageDays=##class(Nur.NIS.Service.Chart.DAO.Chart).getDays(ChartDr)
    Set dayGap=((MeasureDate+1)-admDate)#pageDays
    Set:dischgDate'="" dischDayGap=((dischgDate+1)-admDate)#pageDays
    Quit:(dischgDate'="")&&(MeasureDate>(dischgDate+pageDays-dischDayGap)) date    
    
    Set month=$p($zd(MeasureDate,3),"-",2)
    Set day=+$zd(MeasureDate,4)
    If (MeasureDate=admDate){  //首日显示年月日
    	Set date=$zd(MeasureDate,3)
    	Quit date
    }
    ElseIf (+month=1)&&(day=1) { //跨年显示年月日
    	Set date=$zd(MeasureDate,3)
    	Quit date
    }
    ElseIf (day=1){  //只跨月显示月-日
    	Set date=month_"-01"
    }
    Else {
        If (dayGap=1) {
            //zhuxiaohang
        	Set date=$zd(MeasureDate,3) //非首页的每页第一天显示月/年
        }
        Else{     
        	Set date=+$zd(MeasureDate,4)
        }
    }
    Quit date
}
```