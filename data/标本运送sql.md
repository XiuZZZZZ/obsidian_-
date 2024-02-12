### 标本运送

```sql
-- 标本运送单
-- global:$lg(^Nur.NurseCarryD,piese)
SELECT * FROM Nur.NurseCarry WHERE ID = "20"
	--CarryDR--单号id--涉及表Nur.NurseCarry
	--状态--Status--(C建单子，D删除)
	--交接日期--TransDate--21
	--交接时间--TransTime--22
	--交接人--TransUser-23
	
SELECT * FROM Nur.NurseCarryDetail WHERE LabNo = "1000671958"
	--Status--状态--C-建单中，S-已交接，D-作废，已接收-R ，部分接收-P
	--TransDate--出库日期
	--TransTime--出库时间
```