```
/// 获取最新的身高体重，需要是数字
/// 身高：##class(Nur.Custom.UnitMethod).GetTheLastTemperature("8474297","6")
/// 体重：##class(Nur.Custom.UnitMethod).GetTheLastTemperature("就诊号","35")
ClassMethod GetTheLastTemperature(EpisodeId, ItemId)
{
	s result = ""
	// 获取体征id
	s mrRid = $P(^PAADM(EpisodeId),"^",61)
	// 根据Item和体征id，去倒叙时间日期遍历数据 ($o(^MR(mrAdmId,"OBS",0,"Item",itemId,indexDate,indexTime)))
	// 如果是数字，则返回
	s getIt = 0
	s dateL = "" f {
		s dateL = $o(^MR(mrRid,"OBS",0,"Item",ItemId,dateL),-1)
		q:dateL=""
		q:getIt=1
		s timeL = "" f{
			s timeL = $o(^MR(mrRid,"OBS",0,"Item",ItemId,dateL,timeL),-1)
			q:timeL=""
			q:getIt=1
			s obsId = $o(^MR(mrRid,"OBS",0,"Item",ItemId,dateL,timeL,""))
			s data = ^MR(mrRid,"OBS",obsId)
			s value = $p(data,"^",2)
			i $ISVALIDNUM(value){
				s getIt = 1
				s result = value
			}
		}
	}
	q result
}
```

![[9BEI4Y4~[UGJR4U}@8]JH9N.png]]