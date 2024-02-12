


## main

- 修改
	- [[#获取床位图颜色方法修改]]


## 获取床位图颜色方法修改

```
ClassMethod GetCareLevelColor(EpisodeID)
{
	n (EpisodeID,%session)
	s locID=$p(^PAADM(EpisodeID),"^",4)
	s hospID=$p(^CTLOC(locID),"^",22)
	s level = ##class(Nur.NIS.Service.Base.Patient).GetCareLevel(EpisodeID)
	if (level="特级护理"){
		s colorArr = ##class(Nur.NIS.Service.Base.Bed).GetItemColor("superLevel",hospID) ;特级护理
		i (colorArr'="")&(colorArr.Count()>0) s colorCode = colorArr.GetAt(1).GetAt("ColorCode")
	}elseif(level="一级护理"){
		s colorArr = ##class(Nur.NIS.Service.Base.Bed).GetItemColor("oneLevel",hospID) ;1级护理
		i (colorArr'="")&(colorArr.Count()>0) s colorCode = colorArr.GetAt(1).GetAt("ColorCode")
	}elseif(level="二级护理"){
		s colorArr = ##class(Nur.NIS.Service.Base.Bed).GetItemColor("twoLevel",hospID) ;2级护理
		i (colorArr'="")&(colorArr.Count()>0) s colorCode = colorArr.GetAt(1).GetAt("ColorCode")
	}elseif(level="三级护理"){
		s colorArr = ##class(Nur.NIS.Service.Base.Bed).GetItemColor("threeLevel",hospID) ;3级护理
		i (colorArr'="")&(colorArr.Count()>0) s colorCode = colorArr.GetAt(1).GetAt("ColorCode")
	}
	q colorCode
}
```
