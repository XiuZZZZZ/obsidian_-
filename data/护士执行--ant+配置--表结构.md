```sql
-- 页签id
-- CF.NUR.NIS.ExecuteGroup
SELECT * FROM CF_NUR_NIS.ExecuteGroup WHERE EG_HospDR = "2" AND EG_Type = 1 and EG_Item = "23"
	--安全组或者病区--EG_Type--（1是安全组，2是病区）

-- 页签列表
-- CF.NUR.NIS.ExecuteGroupSub
-- global:$lg(^CF.NUR.NIS.ExecuteGroupSubD(rowId,subRowid),piece)
SELECT EGS_SheetDR->NSS_Name,* FROM CF_NUR_NIS.2 WHERE EGS_ParRef = "1"
	--排序--EGS_SerialId
	--单子--EGS_SheetDR

-- 页签详细信息
-- CF.NUR.NIS.NurseSheetSet
SELECT * FROM CF_NUR_NIS.NurseSheetSet WHERE NSS_ID = "4"
	--

-- 按钮
-- CF.NUR.NIS.ExecButtonColumn
SELECT * FROM CF_NUR_NIS.ExecButtonColumn WHERE EBC_ParRef = "4" AND EBC_Type = "B"
	--类型--EBC_Type--（B：按钮）（C）
	--模板id--EBC_Formwork--
	SELECT * FROM CF_NUR_NIS.SheetPrtSet WHERE ID = "227"
		--打印类型--SPS_ExecSheetDataType

-- 打印绑定内容
-- CF.NUR.NIS.SheetPrtContentSet
-- global $lg(^CF.NUR.NIS.SheetPrtSetD(sheetID,"Content",subID),piece)
SELECT SPCS_DataBindItem,* FROM CF_NUR_NIS.SheetPrtContentSet WHERE SPCS_ParRef = "227"  AND SPCS_TableSetType="OrderInfo"
	--绑定元素--SPCS_DataBindItem--16

--
-- 基础数据--按钮详细信息
-- CF.NUR.NIS.ButtonConfig
SELECT * FROM CF_NUR_NIS.ButtonConfig WHERE ID IN (1,5,6,7,10,35)
```


## 打印
```
打印表结构

```