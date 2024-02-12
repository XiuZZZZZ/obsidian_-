## main

- 点
	- csp：nur.hisui.temperature.csp
	- 页面结构
		- 左：
		- 中：是一个[[#iframe]]，连接到一个aspx服务
		- 右：
	- 获取数据：Nur.NIS.Service.Chart.API.TemperatureChart  getCurve
	- 获取文字内容：Nur.NIS.Service.Chart.DAO.Temperature iteratorCycleVal
	- 获取事件方法：##class(Nur.NIS.Service.Chart.DAO.Temperature).iteratorEvent("432418","16","2")
	- 插入数据--事件：Nur.NIS.Service.Chart.DAO.Event insertEvent


## other


### iframe

```
                    iframeUrl="../../../dhcmg/webchart/IndexForm.aspx?chartType=1&episodeID="+episodeID
                        +"&webIP="+webIP+"&userID="+userID+"&locID="+locID+"&v="+new Date().getTime()+"&rate="+rate+"&fontRate="+fontRate;
                    document.getElementById('iframePicture').src=iframeUrl+"&page="+page+"&chartID=&ifColorPrint="+ifColorPrint; 
```
