

## main

- 点
	- 左下角获取方法：
		- 是csp中，一个是写死的，一个是##class(Nur.SHIFT.Service.ShiftBizV2).GetShiftItem(WardID)
		- 
	- 病区专业组获取方法
		- d ##class(%ResultSet).RunQuery("Nur.NIS.Service.Base.GroupSetting","WardProGroupList","25","","","Y","","1")
		- [[#左边截图]]
	- [[#病区专业组表结构]]
	- 
- 修改
	- [[#增加专业组显示]]
	- [[#点击全部，不显示出院]]


## 病区专业组表结构

```
SELECT * FROM CF_NUR_NIS.WardProGroup WHERE WPG_WardDr = "139" AND WPG_ActivFlag = "Y"

-- CF.NUR.NIS.WardProGroupBed
SELECT * FROM CF_NUR_NIS.WardProGroupBed WHERE WPGB_WardDr = "139" AND WPGB_GroupDr = "96"
```

## 增加专业组显示

csp增加左下角列表:nur.shift.index.csp

```
				<SCRIPT language="cache" RUNAT="SERVER">
					s rows=##class(%ResultSet).%New("Nur.NIS.Service.Base.GroupSetting:WardProGroupList")
					d rows.Execute(WardID,"","","Y","","1")
					while rows.Next()
					{
						s rowProperCount=rows.GetColumnCount()
						s itemName = "专业组"_rows.GetDataByName("desc")
						//s itemName=item.GetDataByName("desc")
						s rowId = rows.GetDataByName("rowid")
						s itemDR="专业组^"_rowId
						s cname="tab"
						i itemDR=ItemDR w "<div title="""_itemName_""" class="""_cname_""" onclick='switchItem(this);reloadDetail("""","""_itemDR_""",1)'>"_itemName_"</div>"
						e  w "<div title="""_itemName_""" class="""_cname_""" onclick='switchItem(this);reloadDetail("""","""_itemDR_""",1)'>"_itemName_"</div>"
					}
				</SCRIPT>
```

列表实现筛选



```
/// ##class(Nur.Z.Need.Shift).GetWardGroupBedIdStr(18)
ClassMethod GetWardGroupBedDescStr(groupId)
{
	s bedDescStr = ""
 	s rowid = ""  f  s rowid=$o(^CF.NUR.NIS.WardProGroupBed(0,"GroupDr",groupId,rowid)) q:rowid=""  d
    .s row=##class(%ArrayOfDataTypes).%New()
    .s group=$p($g(^CF.NUR.NIS.WardProGroup(groupId)),"^",2)
    .s ward=$p($g(^CF.NUR.NIS.WardProGroupBed(rowid)),"^",2)
    .s ward=$s(ward'="":$p($g(^PAWARD(ward)),"^",2),1:"")
    .s pacBed=$p($g(^CF.NUR.NIS.WardProGroupBed(rowid)),"^",3)
    .s bedDesc = $p(^PAWARD(+pacBed,"BED",$p(pacBed,"||",2)),"^",1)
    .s bedDescStr = $s(bedDescStr="":bedDesc,1:bedDescStr_"^"_bedDesc)
	q bedDescStr
}
```


Nur.SHIFT.Service.ShiftBizV2
QueryShiftDetailList
```
    s ifFilterWardGroup = ""
    i ItemDR["专业组" {
		s ifFilterWardGroup = 1
		//获得专业组对应的BedIdStr
		s wardGroupBedDescStr = "^"_##class(Nur.Z.Need.Shift).GetWardGroupBedDescStr($p(ItemDR,"^",2))_"^"
		s ItemDR = ""
	}

	q:(ifFilterWardGroup=1)&&(wardGroupBedDescStr'[bedNo)
```

## 点击全部，不显示出院

修改csp
```
function loadDetailData()
{
	$.cm({
		ClassName: "Nur.SHIFT.Service.ShiftBizV2",
        QueryName: "QueryShiftDetailList",
        WardID: wardID,
        QDate: tjDate,
        ShiftType: shiftType,
        ItemDR: itemSelect,
        ShowAll:showAll,
		rows:99999,
		loadMsg:"加载中...",
	},function(data){
		
		if (itemSelect==""){
			debugger
			var newRows = []
			for (var i=0; i<data.rows.length; i++){
				if(data.rows[i].reason.indexOf("出院")==-1){
					newRows.push(data.rows[i])
				}
			}
			data.rows = newRows
			data.total = newRows.length
		}else{
			
		}

		setDataGrid.datagrid('loadData',data);		 
	})
}
```


## 左边截图

![[Pasted image 20240108150329.png]]