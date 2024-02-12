
# 新增方法

```
​/// ##class(Nur.Custom.DLL).AutoInsertEvent(EpisodeId)
ClassMethod AutoInsertEvent(EpisodeId)
{
    s episodeType = $p(^PAADM(EpisodeId),"^",2)
    i episodeType="E"{
    	//自动带入出观时间 
    	s inOutDateTime = ##class(Nur.Custom.DLL).getInOutDateTime(EpisodeId)
    	s inDate = $p(inOutDateTime,"^",1)
    	s inTime = $p(inOutDateTime,"^",2)
    	s outDate = $p(inOutDateTime,"^",3)
    	s outTime = $p(inOutDateTime,"^",4)
   	 	i inDate'="" d ##class(Nur.NIS.Service.Chart.DAO.Event).EventAutoInsert(EpisodeId,"入观",inDate,inTime)
   	 	i outDate'="" d ##class(Nur.NIS.Service.Chart.DAO.Event).EventAutoInsert(EpisodeId,"出观",outDate,outTime)
	}else{
		//入院
		d ##class(Nur.NIS.Service.Chart.DAO.Event).QtPatAdmDate(EpisodeId)
	}
}
```


# 修改原有调用点

![[Pasted image 20230702230833.png]]