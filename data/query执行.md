```
s ordres=##class(%ResultSet).%New("Nur.SHIFT.Service.ShiftDataBridge:QueryOrdRecord")
d ordres.Execute(wardID,EpisodeID,date)
while ordres.Next()
{
	s columnNum = ordres.GetColumnCount()
	f i=1:1:columnNum{
		s columnName = ordres.GetColumnName(i)
		s columnValue = ordres.GetDataByName(columnName)
	}

}
```