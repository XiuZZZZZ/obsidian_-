

## main

- 概述
- 过程
	- [[#界面放出来诊断]]
	- [[#修改编辑后触发方法对应的后台方法]]
	- [[#获取方法修改]]



## 获取方法修改

Nur.SHIFT.Service.ShiftBizV2  QueryShiftDetailList

```
	.s diagnosis = ##class("Nur.SHIFT.Service.ShiftBizV2").GetDiagIdStr(episodeID,"")
	.s primaryDiagnois = ##class(Nur.Z.Need.Patient).GetprimaryDiags(episodeID)
	.s diagnosis = primaryDiagnois
	.s editDiag = ##class(Nur.Z.Need.Shift).GetShiftEditDiag(episodeID)
	.i editDiag'=""  d
	..s primaryDiagnois = editDiag
	..s diagnosis = editDiag
```


## 修改编辑后触发方法对应的后台方法

Nur.SHIFT.Service.ShiftBizV2
```
ClassMethod SavePatEdit(MainRecordDR, ClsSub, PatSub, Key, Value)
{
	s field=..GetStorageField(Key)
	if Key="diagnois"{
		s episodeId = $p(^Nur.SHIFT.ShiftRecord(MainRecordDR,"C",ClsSub,"P",PatSub) ,"^",1)
		q ##class(Nur.Z.Need.Shift).SetShiftEditDiag(episodeId,Value)
	}
	elseif Key'=""
	{
		q ##class(Nur.SHIFT.Service.ShiftBizBase).SavePatRecordField(MainRecordDR,ClsSub,PatSub,field,Value)
	}
	q 0
}
```

##class(Nur.Custom.NeedUnit.NeedUnit231010).SaveContent

## 界面放出来诊断

搜索columns，然后找到对应位置插入下面数据

```
                {
                    field: 'diagnois', title: '诊断', width: 150, align: 'left', editor: {
                        type: 'text'
                    }
	            }
```

![[Pasted image 20231218232051.png]]