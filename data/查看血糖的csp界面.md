


## main

- 点
	- 远程号
		- 160 110 951
		- 1234qwer
	- [[#不能粘贴复制]]
		- [[#创建一个新的csp]]
		- [[#新建一个js]]
	- [[#初始化一个datagrid]]
	- [[#初始化查询日期时间]]
	- [[#初始化columns]]
	- [[#初始化data]]
	- [[#csp设置datagrid，获取数据]]
	- js的顺序，csp中不同地方server的顺序
	- csp中不同
- 修改
	- [[#引入csp：nur.hisui.bloodToOther.csp]]
	- [[#引入js:bloodToOther.js]]
	- [[#引入class:Nur.Z.Need.BloodToOther]]
	- 提供方法：d ##class(%ResultSet).RunQuery("Nur.Z.Need.BloodToOther","QueryBlood","4")

## 引入class:Nur.Z.Need.BloodToOther

```
Class Nur.Z.Need.BloodToOther Extends %RegisteredObject
{

/// 指定日期范围内，宝贝计划的婴儿数据
/// d ##class(%ResultSet).RunQuery("Nur.Z.Need.BloodToOther","QueryBlood","4")
Query QueryBlood(EpisodeId) As websys.Query(ROWSPEC = "date,url")
{
}

ClassMethod QueryBloodExecute(ByRef qHandle As %Binary, EpisodeId) As %Status
{
	set repid=$I(^CacheTemp)
 	s ind=1
 	s qHandle=$lb(0,repid,0)
	//以下是实例代码
	//第一条数据时间，最后一条数据时间
	s mrRid = $P(^PAADM(EpisodeId),"^",61)
	s startDateL = $o(^MR(mrRid,"OBS",0,"Date",""),1)
	s endDateL = $o(^MR(mrRid,"OBS",0,"Date",""),-1)
	s dataArray = ##class(Nur.Z.Need.BloodToOther).GetData(EpisodeId,startDateL,endDateL)
	s dataArrayNum = dataArray.Count()
	f i=1:1:dataArrayNum{
		s dataObj = dataArray.GetAt(+i)
		s date = dataObj.GetAt("date")
		s url = "http://10.10.16.178/imedical/web/csp/nur.hisui.bloodToOther.csp?EpisodeID="_EpisodeId_"&StartDate="_date_"&EndDate="_date
		d OutQueryBlood
	}
	
	
	
	Set qHandle=$lb(0,repid,0)
	Quit $$$OK
OutQueryBlood
	set Data=$lb(date,url)
 	Set ^CacheTemp(repid,ind)=Data
 	Set ind=ind+1
	quit
}

ClassMethod GetColumnsAndData(EpisodeId, StartDate, EndDate)
{
	s json = ##class(%ArrayOfDataTypes).%New()
	s columns = ..GetColumns()
	s data = ..GetData()
	d json.SetAt(columns,"columns")
	d json.SetAt(data,"data")
	q josn
}

/// ##class(Nur.Z.Need.BloodToOther).GetColumns(2)
ClassMethod GetColumns(HospitalId)
{
	s columns = ##class(%ListOfDataTypes).%New()
	s array = ##class(%ListOfDataTypes).%New()
	d columns.Insert(array)
	
	
	s fieldObj = ##class(%ArrayOfDataTypes).%New()
	d fieldObj.SetAt("date","field")
	d fieldObj.SetAt("日期","title")
	d fieldObj.SetAt(100,"width")
	d array.Insert(fieldObj)
	
	
	//获取血糖项目
	//$o(^MRC("OBITM",0,"Hospital",HospitalId,顺序,itemId))
	s itemIdStr = ..GetBloodItemIdStr(HospitalId)
	f i=1:1:$l(itemIdStr,"^"){
		s itemId = $p(itemIdStr,"^",i)
		continue:itemId=""
		s fieldObj = ##class(%ArrayOfDataTypes).%New()
		s code = $p(^MRC("OBITM",itemId),"^",1)
		s desc = $p(^MRC("OBITM",itemId),"^",2)
		d fieldObj.SetAt(code,"field")
		d fieldObj.SetAt(desc,"title")
		d fieldObj.SetAt(100,"width")
		d array.Insert(fieldObj) 
	}
	q columns
}

ClassMethod GetBloodItemIdStr(HospitalId)
{
	s result = ""
	s index = 0 f{
		s index = $o(^MRC("OBITM",0,"Hospital",HospitalId,index))
		q:index=""
		s itemId = 0 f{
			s itemId = $o(^MRC("OBITM",0,"Hospital",HospitalId,index,itemId))
			q:itemId=""
			//是否血糖
			s type = $p(^MRC("OBITM",itemId),"^",25)
			continue:type'="B"
			//频次
			s time = $p(^MRC("OBITM",itemId),"^",7)
			continue:time'=1
			s result = $s(result="":itemId,1:result_"^"_itemId)
			s sort(index)=itemId
		}
	}
	q result
}

/// ##class(Nur.Z.Need.BloodToOther).GetData(4,"2023-12-12","2024-01-03")
ClassMethod GetData(EpisodeId, StartDate, EndDate)
{
	//{ date: '2024-01-01', time: '08:00', bloodSugar1: 120, bloodSugar2: 110 },
	s dataArray = ##class(%ListOfDataTypes).%New()
	s mrRid = $P(^PAADM(EpisodeId),"^",61)
	s locId = $p($g(^PAADM(EpisodeId)),"^",4)
	s hospId = $p(^CTLOC(locId),"^",22)
	s itemIdStr = ..GetBloodItemIdStr(hospId)
	s startDateL = ##Class(websys.Conversions).DateHtmlToLogical(StartDate)
	s endDateL = ##Class(websys.Conversions).DateHtmlToLogical(EndDate)
	f dateL=startDateL:1:endDateL{
		s idStr = ""
		f i=1:1:$l(itemIdStr,"^"){
			s itemId = $p(itemIdStr,"^",i)
			s id = $o(^MR(mrRid,"OBS",0,"Date",dateL,itemId,""))
			continue:id=""
			s idStr = $s(idStr="":id,1:idStr_"^"_id)
			//s sort(dateL,itemId)=id
		}
		i idStr'=""{
			s dataObj = ##class(%ArrayOfDataTypes).%New()
			d dataObj.SetAt($zd(dateL,3),"date")
			f j=1:1:$l(idStr,"^"){
				s id = $p(idStr,"^",j)
				s itemDr = $p(^MR(mrRid,"OBS",id),"^",1)
				s code = $p(^MRC("OBITM",itemDr),"^",1)
				s value = $p(^MR(mrRid,"OBS",id),"^",2)
				d dataObj.SetAt(value,code)
			}
			d dataArray.Insert(dataObj)
		}
	}
	b
	
	
	q dataArray
}

}

```

## 引入js:bloodToOther.js

```

$(function(){
	initFilterTable()
	initTable()
	initColumns()
	loadTableData()
	initEvent()
})

function initEvent(){
	$('#findBtn').click(function(){
		loadTableData()
	})
}

function initFilterTable(){
	$('#startDate').datebox('setValue', StartDate);
	$('#endDate').datebox('setValue', EndDate);
	
}

function initTable(){
	$('#bloodDg').datagrid();
}


function initColumns(){
	$cm({
	    ClassName:"Nur.Z.Need.BloodToOther",
	    MethodName:"GetColumns",
	    HospitalId:session["LOGON.HOSPID"]
	},function(columns){
		$('#bloodDg').datagrid({
			columns: columns
		});
	});
}



function loadTableData(){
	
	
	$cm({
	    ClassName:"Nur.Z.Need.BloodToOther",
	    MethodName:"GetData",
	    EpisodeId:EpisodeId,
	    StartDate:$('#startDate').datebox('getValue'),
	    EndDate:$('#endDate').datebox('getValue'),
	},function(data){
		$('#bloodDg').datagrid({
			data: data
		});
	});
}
```

## 引入csp：nur.hisui.bloodToOther.csp

```
<csp:method name=OnPreHTTP
            arguments=""
            returntype=%Boolean>
    i ##Class(ext.websys.SessionEvents).SessionExpired() q 1 
    q 1
</csp:method>

<script language="cache" runat="SERVER">
	s StartDate = $G(%request.Data("StartDate", 1))
	s EndDate = $G(%request.Data("EndDate", 1))
	s EpisodeId = $G(%request.Data("EpisodeID", 1))
</script>

<HTML lang="zh-CN">
<head>
    <meta http-equiv="Content-Type"
          content="text/html; charset=utf-8">
    <title>
        <EXTHEALTH:TRANSLATE id=title>##(%session.Get("TITLE"))##</EXTHEALTH:TRANSLATE>
    </title>
    <EXTHEALTH:HEAD></EXTHEALTH:HEAD>
    <HISUI translate=1/>
    
    <script type="text/javascript" src="../scripts/nurse/hisui/bloodToOther.js" charset=gbk></script>
    
	<style type='text/css'>
        body {
	        background-color:#fff;
            padding:0;
            margin:0;
        }
	</style>
	
    <Server>
    //获取撤销、停止执行记录原因json
	//s OECStatusChReasonJson=##Class(web.DHCDocInPatUICommon).GetOECStatusChReason()
	</Server>
</head>

<body>
	<table>
		<tr>
			<td></td>
			<td>
				<label>开始日期</label>
				<input id="startDate" class="hisui-datebox textbox datebox-f combo-f" style="display: none;">
			</td>
			<td>
				<label>结束日期</label>
				<input id="endDate" class="hisui-datebox textbox datebox-f combo-f" style="display: none;">
			</td>
			<td>
				<a id="findBtn" href="#" class="hisui-linkbutton" data-options="iconCls:'icon-w-find'" style="width:100px">查询</a>
			</td>
		</tr>
	</table>
	<div id="bloodDg">1</div>

<script language="javascript">
	var StartDate = "#(StartDate)#"
	var EndDate = "#(EndDate)#"
	var EpisodeId ="#(EpisodeId)#";
</script>


</body>
<script language="cache"  runat="SERVER">
        //s userID=%session.Get("LOGON.USERID")
        //w "<script type='text/javascript' src='../scripts/dhcdoc/ipdoc/InPatOrderView.js'/></script"_">"

</script>
</html>
```


## 初始化data

```

```

## 初始化columns

```
function initColumns(){
	$cm({
	    ClassName:"Nur.Z.Need.BloodToOther",
	    MethodName:"GetColumns",
	    HospitalId:session["LOGON.HOSPID"]
	},function(columns){
		$('#bloodDg').datagrid({
			columns: columns
		});
	});
}

/// ##class(Nur.Z.Need.BloodToOther).GetColumns(2)
ClassMethod GetColumns(HospitalId)
{
	s columns = ##class(%ListOfDataTypes).%New()
	s array = ##class(%ListOfDataTypes).%New()
	d columns.Insert(array)
	
	
	s fieldObj = ##class(%ArrayOfDataTypes).%New()
	d fieldObj.SetAt("date","field")
	d fieldObj.SetAt("日期","title")
	d fieldObj.SetAt(100,"width")
	d array.Insert(fieldObj)
	
	
	//获取血糖项目
	//$o(^MRC("OBITM",0,"Hospital",HospitalId,顺序,itemId))
	s index = 0 f{
		s index = $o(^MRC("OBITM",0,"Hospital",HospitalId,index))
		q:index=""
		s itemId = 0 f{
			s itemId = $o(^MRC("OBITM",0,"Hospital",HospitalId,index,itemId))
			q:itemId=""
			//是否血糖
			s type = $p(^MRC("OBITM",itemId),"^",25)
			continue:type'="B"
			//频次
			s time = $p(^MRC("OBITM",itemId),"^",7)
			continue:time'=1
			
			s fieldObj = ##class(%ArrayOfDataTypes).%New()
			s code = $p(^MRC("OBITM",itemId),"^",1)
			s desc = $p(^MRC("OBITM",itemId),"^",2)
			d fieldObj.SetAt(code,"field")
			d fieldObj.SetAt(desc,"title")
			d fieldObj.SetAt(100,"width")
			d array.Insert(fieldObj) 
			s sort(index)=itemId
		}
	}
	q columns
}
```



## csp设置datagrid，获取数据

## 不能粘贴复制


## 初始化查询日期时间

```
	<table>
		<tr>
			<td></td>
			<td>
				<label>开始日期</label>
				<input id="startDate" class="hisui-datebox textbox datebox-f combo-f" style="display: none;">
			</td>
			<td>
				<label>结束日期</label>
				<input id="endDate" class="hisui-datebox textbox datebox-f combo-f" style="display: none;">
			</td>
			<td>
				<a href="#" class="hisui-linkbutton" data-options="iconCls:'icon-w-find'" style="width:100px">查询</a>
			</td>
		</tr>
	</table>
```

## 初始化一个datagrid

```
<div id="bloodDg">1</div>

function initTable(){
  $('#bloodDg').datagrid();
}

```

## 新建一个js

然后引入

```
$(function(){
	initTable()
})
```

## 创建一个新的csp

```
<csp:method name=OnPreHTTP
            arguments=""
            returntype=%Boolean>
    i ##Class(ext.websys.SessionEvents).SessionExpired() q 1 
    q 1
</csp:method>

<script language="cache" runat="SERVER">
</script>

<HTML lang="zh-CN">
<head>
    <meta http-equiv="Content-Type"
          content="text/html; charset=utf-8">
    <title>
        <EXTHEALTH:TRANSLATE id=title>##(%session.Get("TITLE"))##</EXTHEALTH:TRANSLATE>
    </title>
    <EXTHEALTH:HEAD></EXTHEALTH:HEAD>
    <HISUI translate=1/>
    
    <script type="text/javascript" src="../scripts/DHC.WMR.PinYinHead.js"></script>
    
	<style type='text/css'>
        body {
	        background-color:#fff;
            padding:0;
            margin:0;
        }
	</style>
	
    <Server>
    //获取撤销、停止执行记录原因json
	//s OECStatusChReasonJson=##Class(web.DHCDocInPatUICommon).GetOECStatusChReason()
	</Server>
</head>

<body>

	<div id="bloodDg">1</div>

<script language="javascript">
	///var EpisodeID ="#(
	///EpisodeID)#";
</script>


</body>
<script language="cache"  runat="SERVER">
        //s userID=%session.Get("LOGON.USERID")
        //w "<script type='text/javascript' src='../scripts/dhcdoc/ipdoc/InPatOrderView.js'/></script"_">"

</script>
</html>
```