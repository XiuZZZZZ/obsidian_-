
# 过程

- 确定索引
	- [[20231120--现有出院索引1--当前科室，出院日期]]
	- [[20231120--现有出院索引2]]
	- [[20231120--现有出院索引3]]
	- [[20231120--现有出院索引4]]
- 测试科室出院病人
- 修改前台：nur.hisui.patlist.panel.csp
	- 内容参考滇东北
- 修改后台：##class(Nur.NIS.Service.Base.InPatientList).getInPatQuery
	- 




## 测试科室出院病人

![[Pasted image 20231120121425.png]]

## 写方法

## 修改前台



![[Pasted image 20231120154244.png]]

## 需改后台




```
	i Type="Out" {
		s curDateL = +$h
		f disDate=(curDateL-50):1:curDateL {
			s episodeID = "" f{
				s episodeID = $o(^PAADMi("DischDate",disDate,episodeID))
				q:episodeID=""
				s PAPMI = $p(^PAADM(episodeID),"^",1)
				s wardId = $p($g(^PAADM(episodeID)),"^",70)
				continue:wardId'=WardID
				s bedCode="",wardDesc=""
				s bedSub=$p($g(^PAADM(episodeID)),"^",73)
				i bedSub'="" d
				.s curWardId=$P(bedSub,"||",1)
				.s curBedSub=$P(bedSub,"||",2)
				.s bedCode=$p($g(^PAWARD(curWardId,"BED",curBedSub)),"^",1)
				.s wardDesc=$p($g(^PAWARD(curWardId)),"^",2)
				d getPatInfo
				d outPutPat
			}
		}

	}
```