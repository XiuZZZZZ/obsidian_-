
## aa

- 过程
	- 查看医嘱单顺序现在绑定的医嘱日期是什么 [[#绑定的是什么]]
		- 绑定的是医嘱开始时间
	- 改顺序 ：
		- 把时间顺序加进去



## 绑定的是什么
```sql
-- 医嘱单配置表
-- CF.NUR.POS.OrderSheetSet
SELECT * FROM CF_NUR_POS.OrderSheetSet WHERE HospDR = "2"

-- CF.NUR.POS.SheetConfig
SELECT * FROM CF_NUR_POS.SheetConfig WHERE Parref = "1" AND type = "L"

'128'
-- 基础数据
-- CT.NUR.NIS.NurseBasicData
SELECT * FROM CT_NUR_NIS.NurseBasicData WHERE ID = "128"
'##class(Nur.NIS.Service.OrderSheet.Sheet).getOrdDate(OeordId, OeordSub, CONFIG)'
```

## 现象
![[Pasted image 20231219220159.png]]

## 结果
![[Pasted image 20231219220137.png]]