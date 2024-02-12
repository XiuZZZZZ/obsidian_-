

## main

- 过程
	- 输血巡视入口
		- page/patrol/biz/list/getWardBloodPatrolInfo  Nur.NIS.Service.Patrol.List  getWardBloodPatrolInfo
	- [[#表是什么]]
	- 时间是取的什么
	- 找到一个数据


## 表是什么

```
-- pda输血巡视
-- gloabl:$lg(^Nur.BloodBagReciveD(id),piece)
SELECT TransStartDate,TransEdDate,* FROM Nur.BloodBagRecive
	--输血开始日期--TransStartDate--13
	--输血开始时间--TransStartTime--14
	--输血结束日期--TransEdDate--15
	--输血结束时间--TransEdTime--16
```