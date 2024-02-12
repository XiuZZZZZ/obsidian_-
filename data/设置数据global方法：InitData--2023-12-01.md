
```
ClassMethod InitData()
{
	k ^NurMpToShift
	//【vte内】
	//单子code：DHCNURNKZYHZJMXSSSZFXPGJYFCSB3
	//单子类型：里面多个日期时间列数据
	s ^NurMpToShift("itemValueSort","DHCNURNKZYHZJMXSSSZFXPGJYFCSB3","lineIndex",1) = "CareDate,CareTime,Item86"
	s ^NurMpToShift("itemValueSort","DHCNURNKZYHZJMXSSSZFXPGJYFCSB3","lineIndex",2) = "Item23,Item24,Item87"
	s ^NurMpToShift("itemValueSort","DHCNURNKZYHZJMXSSSZFXPGJYFCSB3","lineIndex",3) = "Item25,Item28,Item88"
	s ^NurMpToShift("itemValueSort","DHCNURNKZYHZJMXSSSZFXPGJYFCSB3","lineIndex",4) = "Item26,Item29,Item89"
	s ^NurMpToShift("itemValueSort","DHCNURNKZYHZJMXSSSZFXPGJYFCSB3","lineIndex",5) = "Item27,Item30,Item90"
	//判断标准：>=4
	s ^NurMpToShift("itemValueSort","DHCNURNKZYHZJMXSSSZFXPGJYFCSB3","baseData","scoreLevel")=4
	s ^NurMpToShift("itemValueSort","DHCNURNKZYHZJMXSSSZFXPGJYFCSB3","baseData","condition")=">="
}
```