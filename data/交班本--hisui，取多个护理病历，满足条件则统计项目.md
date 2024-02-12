

# 过程

## 远程号

376 292 962
dn0fee2p

## 维护global

```
	/// 【成人跌倒1】  ProcessNurMp2("DHCNURCRZYHZDDFXPGJYFCSB@DHCNURzyetddfxjhlcsb1")
	//单子code：DHCNURCRZYHZDDFXPGJYFCSB
	//单子类型：里面多个日期时间列数据
	s ^NurMpToShift("itemValueSort","DHCNURCRZYHZDDFXPGJYFCSB","lineIndex",1) = "CareDate,CareTime,Item48"
	s ^NurMpToShift("itemValueSort","DHCNURCRZYHZDDFXPGJYFCSB","lineIndex",2) = "Item192,Item196,Item49"
	s ^NurMpToShift("itemValueSort","DHCNURCRZYHZDDFXPGJYFCSB","lineIndex",3) = "Item197,Item198,Item50"
	s ^NurMpToShift("itemValueSort","DHCNURCRZYHZDDFXPGJYFCSB","lineIndex",4) = "Item199,Item120,Item51"
	s ^NurMpToShift("itemValueSort","DHCNURCRZYHZDDFXPGJYFCSB","lineIndex",5) = "Item201,Item202,Item52"
	//判断标准：>=5
	s ^NurMpToShift("itemValueSort","DHCNURCRZYHZDDFXPGJYFCSB","baseData","scoreLevel")=46
	s ^NurMpToShift("itemValueSort","DHCNURCRZYHZDDFXPGJYFCSB","baseData","condition")=">="
	
	/// 【儿童跌倒2】
	//单子code：DHCNURzyetddfxjhlcsb1
	//单子类型：里面多个日期时间列数据
	s ^NurMpToShift("itemValueSort","DHCNURzyetddfxjhlcsb1","lineIndex",1) = "Item13,Item17,Item45"
	s ^NurMpToShift("itemValueSort","DHCNURzyetddfxjhlcsb1","lineIndex",2) = "Item14,Item18,Item46"
	s ^NurMpToShift("itemValueSort","DHCNURzyetddfxjhlcsb1","lineIndex",3) = "Item15,Item19,Item47"
	s ^NurMpToShift("itemValueSort","DHCNURzyetddfxjhlcsb1","lineIndex",4) = "Item16,Item20,Item48"
	//判断标准：>=5
	s ^NurMpToShift("itemValueSort","DHCNURzyetddfxjhlcsb1","baseData","scoreLevel")=12
	s ^NurMpToShift("itemValueSort","DHCNURzyetddfxjhlcsb1","baseData","condition")=">="
```

## 维护配置

ProcessNurMp2("DHCNURCRZYHZDDFXPGJYFCSB@DHCNURzyetddfxjhlcsb1")