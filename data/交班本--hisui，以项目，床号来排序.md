


## 过程
- 过程
	- 打印的排序
		- 方法：Nur.SHIFT.Service.Emr.EmrService  QueryShiftDetailListV2
	- 修改
		- 增加方法  获得指定班次病人的全部项目 [[#获得指定班次病人的全部项目]]
		- 修改打印获取数据方法
			- 修改明细方法
				- 增加一个columns
				- s allreason = ##class(Nur.Z.Need.Shift).GetAllReason(mainRecordDR,episodeID)
			- 修改打印获取数据方法  [[#修改打印获取数据方法]]


## 修改打印获取数据方法

```

s allreason=resultInfo.GetDataByName("allreason")  
s firstReason=$p(allreason,",",1)

			f i=1:1:count
			{
				s columnName=resultInfo.GetColumnName(i)
				i columnName="reason" {
					s value = resultInfo.GetDataByName("allreason")
				}else{
					s value = resultInfo.GetDataByName(columnName)
				}
				
				d tmpResult.SetAt(value,columnName)
			}
```

## 获得指定班次病人的全部项目

```
/// 根据班次和病人，获得全部项目
/// ##class(Nur.Z.Need.Shift).GetAllReason(12204,1009396)
ClassMethod GetAllReason(mainRecordDR, episodeId)
{

	s itemDRStr="",itemStr=""
	s clsSub=0 f {
		s clsSub=$o(^Nur.SHIFT.ShiftRecord(mainRecordDR,"C",clsSub))
		q:clsSub=""
		s patSub = $o(^Nur.SHIFT.ShiftRecordi(0,"PatAdm",episodeId,mainRecordDR,clsSub,""))
		s itemSub=0 f  s itemSub=$o(^Nur.SHIFT.ShiftRecord(mainRecordDR,"C",clsSub,"P",patSub,"I",itemSub)) q:itemSub=""  d
		.s itemData=$g(^Nur.SHIFT.ShiftRecord(mainRecordDR,"C",clsSub,"P",patSub,"I",itemSub))
		.q:itemData=""
		.q:($p(itemData,"^",5)="N")!($p(itemData,"^",5)="A")
		.s itemDR=$p(itemData,"^",1)
		.s itemInfo=$g(^CF.NUR.SHIFT.ExchangeItem(itemDR))
		.q:itemInfo=""
		.s detailFlag=$p(itemInfo,"^",16)
		.q:(detailFlag="N")
		.s validWard=$p(itemInfo,"^",9)
		.s invalidWard=$p(itemInfo,"^",10)
		.s:validWard'="" validWard="|"_validWard_"|"
		.s:invalidWard'="" invalidWard="|"_invalidWard_"|"
		.q:(validWard'="")&&(validWard'[("|"_WardID_"|"))
		.q:(invalidWard'="")&&(invalidWard[("|"_WardID_"|"))
		.s itemSort=$p(itemInfo,"^",6)
		.s:itemSort="" itemSort=999
		.s itemName=$p(itemInfo,"^",1)
		.s items(itemSort,itemDR)=itemName
	}
	s sort="" f  s sort=$o(items(sort)) q:sort=""  d
	.s dr="" f  s dr=$o(items(sort,dr)) q:dr=""  d
	..i itemDRStr="" s itemDRStr=dr
	..e  s itemDRStr=itemDRStr_"^"_dr
	..i itemStr="" s itemStr=items(sort,dr)
	..e  s itemStr=itemStr_","_items(sort,dr)
	q itemStr
}
```