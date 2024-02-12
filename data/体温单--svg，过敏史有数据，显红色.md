
## 修改

### 增加方法

```
##class(Nur.NIS.Service.Chart.DAO.Temperature).ifContentToRed(ifAllergiesCycleSub,ifHasAllergies,content)
ClassMethod ifContentToRed(EpisodeID, CycleSub, StartDate)
{
	s ^||ifAllergiesRedColor = 0
	i CycleSub="656" {
		s EndDate = StartDate+6
		f date=StartDate:1:EndDate{
			s value = ##class(Nur.Custom.Temperature).getMRData(EpisodeID,"170",date,2)
			i (value'="")&&(value'="无"){
				s ^||ifAllergiesRedColor = 1
			}
		}
	}
	q ""
}
```

### 修改方法1

增加代码行
d ##class(Nur.NIS.Service.Chart.DAO.Temperature).ifContentToRed(EpisodeID,CycleSub,MeasureDate)

```
///##class(Nur.NIS.Service.Chart.DAO.Expression).excuteExpression()
ClassMethod excuteExpression(EpisodeID, MeasureDate, ChartDr, CycleSub, Day = "", TimePos = "") As Nur.NIS.Service.Chart.Base.Text
{
    New (EpisodeID,MeasureDate,ChartDr,CycleSub,Day,TimePos)
    Set ^tempsc("excuteExpression")=$lb(EpisodeID,MeasureDate,ChartDr,CycleSub,Day,TimePos)
    Set expression=$LG(^CT.NUR.NIS.ChartD(ChartDr,"ChildCycleVal",CycleSub),3)
    Set loopTimes=$LG(^CT.NUR.NIS.ChartD(ChartDr,"ChildCycleVal",CycleSub),16)
    Set classname=$p(expression,"^",1)
    Set methodname=$p(expression,"^",2)
    Set itemcode=$p(expression,"^",3)
    Set content=..getContent(ChartDr,EpisodeID,MeasureDate,classname,methodname,itemcode,loopTimes,Day,TimePos)
    s ifAllergiesCycleSub = ##class(Nur.NIS.Service.Chart.DAO.Temperature).ifAllergiesCycleSub(CycleSub)
	d ##class(Nur.NIS.Service.Chart.DAO.Temperature).ifContentToRed(EpisodeID,CycleSub,MeasureDate)
    Set xAxis=..calcXAxis(ChartDr, CycleSub,Day,TimePos,loopTimes)
    Set yAxis=..calcYAxis(ChartDr, CycleSub,Day,TimePos)
    Set text=..newTextByChartD(ChartDr,CycleSub,content, xAxis, yAxis)
    Quit text
}
```


### 修改方法2

增加代码行
```
	i (^||ifAllergiesRedColor){  
		s color = "#ff1e02"  
	}
```

```
///##class(Nur.NIS.Service.Chart.DAO.Expression).excuteExpression()
ClassMethod newTextByChartD(ChartDr, CycleSub, Content, XAxis, YAxis) As Nur.NIS.Service.Chart.Base.Text  
{  
	New (ChartDr, CycleSub, Content, XAxis,YAxis)  
	;Set desc=$LG(^CT.NUR.NIS.ChartD(ChartDr,"ChildCycleVal",CycleSub),2)  
	;Set x=$LG(^CT.NUR.NIS.ChartD(ChartDr,"ChildCycleVal",CycleSub),4)  
	;Set y=$LG(^CT.NUR.NIS.ChartD(ChartDr,"ChildCycleVal",CycleSub),5)  
	;Set width=$LG(^CT.NUR.NIS.ChartD(ChartDr,"ChildCycleVal",CycleSub),7)  
	Set alignment=$LG(^CT.NUR.NIS.ChartD(ChartDr,"ChildCycleVal",CycleSub),15)  
	Set fontFamily=$LG(^CT.NUR.NIS.ChartD(ChartDr,"ChildCycleVal",CycleSub),9)  
	Set fontSize=$LG(^CT.NUR.NIS.ChartD(ChartDr,"ChildCycleVal",CycleSub),14)  
	Set fontStyle=$LG(^CT.NUR.NIS.ChartD(ChartDr,"ChildCycleVal",CycleSub),17)  
	Set color=$LG(^CT.NUR.NIS.ChartD(ChartDr,"ChildCycleVal",CycleSub),19)  
	Set:color="" color=$LG(^CT.NUR.NIS.ChartD(ChartDr,"ChildCycleVal",CycleSub),12)  
	i (^||ifAllergiesRedColor){  
		s color = "#ff1e02"  
	}  
	Set itemWidth=$LG(^CT.NUR.NIS.ChartD(ChartDr,"ChildCycleVal",CycleSub),13)  
	;Set xVal=(Position-1)*itemWidth+x+width  
	Set text=##class(Nur.NIS.Service.Chart.Base.Text).%New(Content, XAxis, YAxis,$g(fontFamily), $g(fontSize), $g(fontStyle), $g(color),$g(itemWidth),$g(alignment))  
	Quit text  
}
```